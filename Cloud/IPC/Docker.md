---
title: Infomaniak Public Cloud - Mise en situation : Environnement Docker
description: Mettre en place un environnement Docker avec Traefik et Portainer
published: true
date: 2021-11-12T09:52:54.145Z
tags: openstack, docker, infomaniak, public-cloud, cloud, ipc, heat
editor: markdown
dateCreated: 2021-11-12T08:15:12.116Z
---

![plan_de_travail_1@4x.png](/images/cloud/infomaniak-public-cloud/plan_de_travail_1@4x.png =600x)

# Introduction
J'ai pour habitude de déployer l'ensemble de mes services dans des containers Dockers. C'est donc tout naturellement que je vous propose de créer un environnement Docker comme je les aimes directement dans le Public Cloud !
Nous procéderons à l'installation de **Traefik** et de **Portainer** afin de gérer nos containers avec une **interface web** et d'**utiliser du SSL** pour l'ensemble des futurs services.
 Vous pouvez utilisez le **template Heat** directement ou faire son installation à la main.

## Quelques liens utiles :
- [🐳 Docker *Découvrir la conteneurisation avec Docker*](/Conteneurisation#docker)
- [💙 Traefik 🚧 *Un reverse-proxy pour tous les services*](/Conteneurisation/Docker/Traefik)
- [❤️ Portainer *Une interface web pour gérer Docker*](/Conteneurisation/Docker/Portainer)
- [💙 SelfHosted *Plein de solutions que vous pouvez héberger vous même !*](/SelfHosted)
{.links-list}

# Template Heat
## Créez le fichier `docker.yml`:
```yaml
heat_template_version: rocky
description:  Docker Environment
parameters:
  image:
    type: string
    description: image use for docker
    default: Debian 11.1 bullseye
  key:
    type: string
    description: SSH key to connect to the servers
  flavor:
    type: string
    description: flavor used by docker
    default: a4-ram8-disk50-perf1
  network:
    type: string
    description: network used by docker
    default: docker-network
  subnet_id:
    type: string
    description: dedicated subnet for docker
    default: docker-subnet
  floating_network_id:
    type: string
    description: UUID of a Neutron external network
    default: ext-floating1
  EMAIL:
    type: string
    description: Email for Let's Encrypt
  NDD:
    type: string
    description: Domain for URL (example.com)

resources:
  wait_condition:
    type: OS::Heat::WaitCondition
    properties:
      handle: { get_resource: wait_handle }
      timeout: 600

  wait_handle:
    type: OS::Heat::WaitConditionHandle
  
  random_password_traefik:
    type: OS::Heat::RandomString
    properties:
      length: 16


  # security group
  docker_security_group:
    type: OS::Neutron::SecurityGroup
    properties:
      name: "docker_security_group"
      description: >
        Allows ICMP, SSH, HTTP & HTTPS default port
      rules:
         - { direction: ingress, protocol: icmp }
         - { direction: ingress, protocol: tcp, port_range_min: 22, port_range_max: 22 }
         - { direction: ingress, protocol: tcp, port_range_min: 80, port_range_max: 80 }
         - { direction: ingress, protocol: tcp, port_range_min: 443, port_range_max: 443 }

  # network resources
  docker_network:
    type: OS::Neutron::Net
    properties:
      name: { get_param: network }
      value_specs:
        mtu: 1500

  docker_subnet:
    type: OS::Neutron::Subnet
    properties:
      name: 'docker-subnet'
      network_id: { get_resource: docker_network }
      cidr: "10.11.3.0/24"
      dns_nameservers:
        - "84.16.67.69"
        - "84.16.67.70"
      ip_version: 4

  docker_router:
    type: OS::Neutron::Router
    properties:
      name:  'docker-router'
      external_gateway_info: { network: ext-floating1 }

  docker_router_subnet_interface:
    type: OS::Neutron::RouterInterface
    properties:
      router_id: { get_resource: docker_router }
      subnet: { get_resource: docker_subnet }

  docker_port:
    type: OS::Neutron::Port
    properties:
      network: { get_resource: docker_network }
      security_groups: [ { get_resource: docker_security_group } ]
      fixed_ips:
        - subnet_id: { get_resource: docker_subnet }

  docker_floating:
    type: OS::Neutron::FloatingIP
    properties:
      floating_network_id: { get_param: floating_network_id }
      port_id: { get_resource: docker_port }

  # instance
  server:
    type: OS::Nova::Server
    depends_on: [ docker_router]
    properties:
      flavor: { get_param: flavor }
      image: { get_param: image }
      key_name: {get_param: key}
      networks:
        - port: { get_resource: docker_port }
      user_data:
        str_replace:
          template: |
            #!/bin/bash -v
            apt update && apt upgrade -y
            apt install -y curl apt-transport-https ca-certificates gnupg2 software-properties-common apache2-utils
            curl -fsSL https://download.docker.com/linux/$(. /etc/os-release; echo "$ID")/gpg | apt-key add -
            add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/$(. /etc/os-release; echo "$ID") $(lsb_release -cs) stable"
            apt-get update
            apt-get -y install docker-ce docker-compose
            systemctl enable docker
            systemctl start docker
            mkdir -p /apps/traefik
            cat <<EOF >>/apps/traefik/traefik.yml
            api:
              dashboard: true
            entryPoints:
              http:
                address: ":80"
              https:
                address: ":443"
            providers:
              docker:
                endpoint: "unix:///var/run/docker.sock"
                exposedByDefault: false
            certificatesResolvers:
              http:
                acme:
                  email: $EMAIL
                  storage: acme.json
                  httpChallenge:
                    entryPoint: http
            providers.file:
                filename: "/etc/traefik/dynamic_conf.toml"
                watch: true
            EOF
            cat <<EOF >>/apps/traefik/config.yml
            http:
              middlewares:
                https-redirect:
                  redirectScheme:
                    scheme: https
                default-headers:
                  headers:
                    frameDeny: true
                    sslRedirect: true
                    browserXssFilter: true
                    contentTypeNosniff: true
                    forceSTSHeader: true
                    stsIncludeSubdomains: true
                    stsPreload: true
                secured:
                  chain:
                    middlewares:
                    - default-headers
            EOF
            touch /apps/traefik/acme.json
            chmod 600 /apps/traefik/acme.json
            docker network create proxy
            USERPASS=$(echo $(htpasswd -nb admin $PASSWORD_TRAEFIK) | sed -e s/\\$/\\$\\$/g)
            cat <<EOF >>/apps/traefik/docker-compose.yml
            version: '2'
            services:
              traefik:
                image: traefik:latest
                container_name: traefik
                restart: unless-stopped
                security_opt:
                  - no-new-privileges:true
                networks:
                  - proxy
                ports:
                  - 80:80
                  - 443:443
                volumes:
                  - /etc/localtime:/etc/localtime:ro
                  - /var/run/docker.sock:/var/run/docker.sock:ro
                  - /apps/traefik/traefik.yml:/traefik.yml:ro
                  - /apps/traefik/acme.json:/acme.json
                  - /apps/traefik/config.yml:/config.yml:ro
                labels:
                  - traefik.enable=true
                  - traefik.http.routers.traefik.entrypoints=http
                  - traefik.http.routers.traefik.rule=Host("traefik.$NDD")
                  - traefik.http.middlewares.traefik-auth.basicauth.users=$USERPASS
                  - traefik.http.middlewares.traefik-https-redirect.redirectscheme.scheme=https
                  - traefik.http.routers.traefik.middlewares=traefik-https-redirect
                  - traefik.http.routers.traefik-secure.entrypoints=https
                  - traefik.http.routers.traefik-secure.rule=Host("traefik.$NDD")
                  - traefik.http.routers.traefik-secure.middlewares=traefik-auth
                  - traefik.http.routers.traefik-secure.tls=true
                  - traefik.http.routers.traefik-secure.tls.certresolver=http
                  - traefik.http.routers.traefik-secure.service=api@internal
            networks:
              proxy:
                external: true
            EOF
            docker-compose -f /apps/traefik/docker-compose.yml up -d
            mkdir -p /apps/portainer
            cat <<EOF >>/apps/portainer/docker-compose.yml
            version: '2'
            services:
              portainer:
                image: portainer/portainer-ce:latest
                container_name: portainer
                restart: unless-stopped
                security_opt:
                  - no-new-privileges:true
                environment:
                  TEMPLATES: https://github.com/PAPAMICA/docker-compose-collection/blob/master/templates-portainer.json
                networks:
                  - proxy
                volumes:
                  - /etc/localtime:/etc/localtime:ro
                  - /var/run/docker.sock:/var/run/docker.sock:ro
                  - /apps/portainer/data:/data
                labels:
                  - traefik.enable=true
                  - traefik.http.routers.portainer.entrypoints=http
                  - traefik.http.routers.portainer.rule=Host("portainer.$NDD")
                  - traefik.http.middlewares.portainer-https-redirect.redirectscheme.scheme=https
                  - traefik.http.routers.portainer.middlewares=portainer-https-redirect
                  - traefik.http.routers.portainer-secure.entrypoints=https
                  - traefik.http.routers.portainer-secure.rule=Host("portainer.$NDD")
                  - traefik.http.routers.portainer-secure.tls=true
                  - traefik.http.routers.portainer-secure.tls.certresolver=http
                  - traefik.http.routers.portainer-secure.service=portainer
                  - traefik.http.services.portainer.loadbalancer.server.port=9000
                  - traefik.docker.network=proxy
                
            networks:
              proxy:
                external: true
            EOF
            docker-compose -f /apps/portainer/docker-compose.yml up -d

            wc_notify --data-binary '{"status": "SUCCESS"}'

          params:
            wc_notify: { get_attr: ['wait_handle', 'curl_cli'] }
            $NDD: { get_param: NDD }
            $EMAIL: { get_param: EMAIL }
            $PASSWORD_TRAEFIK:  { get_attr: [random_password_traefik, value] }


outputs:
  traefik_url:
    value:
      str_replace:
        template: https://traefik.NDD (admin / PASSWORD)
        params:
          NDD: { get_param: NDD }
          PASSWORD: { get_attr: [random_password_traefik, value] }
    description: "Traefik URL"

  portainer_url:
    value:
      str_replace:
        template: https://portainer.NDD
        params:
          NDD: { get_param: NDD }
    description: "Portainer URL"

  server_IP:
    value: 
      str_replace:
        template: /!\ Don't forget to redirect traefik.NDD and portainer.NDD to IP !
        params:
          NDD: { get_param: NDD }
          IP: { get_attr: [docker_floating, floating_ip_address] }
    description: "IP"
```
## Déployer la stack
Lancez la création de la stack avec la commande suivante :
```bash
openstack stack create --template docker.yml docker --parameter key=<KEY> --parameter EMAIL=<EMAIL> --parameter NDD=<NDD>
```


> ***KEY***
> - Nom ou ID de votre clé SSH
>
> ***EMAIL***
> - Votre adresse mail pour la génération des certificats Let's Encrypt
>
> ***NDD***
> - Votre nom de domaine (exemple : `papamica.com`)
>
> 	.
{.is-info}

> Pensez à faire les redirections `traefik.NDD` et `portainer.NDD` vers l'adresse IP de votre instance !
> Voir `openstack stack show docker`
{.is-warning}

> Votre environnement Docker est prêt !
> Vous pouvez accéder à l'interface de Traefik et Portainer sur leur URL respective.
> Vus retrouverez les identifiants de connexion de Traefik avec la commande `openstack stack show docker`
{.is-success}



Pour la gestion de la stack, voir ce tutoriel :
  - [🔥 L'orchestration *Créer et gérer les stacks Heat*](/Cloud/IPC/Orchestration)
{.links-list}


# Création de la VM
## Horizon
### Création du groupe de sécurité pour la connexion HTTP et HTTPS
Pour le fonctionnement de Traefik et pouvoir acceder à vos services, vous devez ouvrir le port HTTP (`80`) et HTTPS (`443`). Si vous avez bien suivis les autres tutoriel, cela devrais etre simple. Créez donc un nouveau groupe de sécurité `HTTP-HTTPS`en suivant ce tutoriel :

  - [🛡️ Les groupes de sécurité *Comprendre et utiliser le firewall et ses règles avec Horizon*](/Cloud/IPC/Security-Groups#horizon)
{.links-list}

Et ajoutez les règles suivantes : 
![infomaniak-publiccloud_10_1.png](/images/cloud/infomaniak-public-cloud/10/infomaniak-publiccloud_10_1.png =600x)
![infomaniak-publiccloud_10_2.png](/images/cloud/infomaniak-public-cloud/10/infomaniak-publiccloud_10_2.png =600x)

### Création de l'instance
Comme pour la création du groupe de sécurité, utilisez le tutoriel suivant pour la création de l'instance : 
 - [⚡ Les instances (machines virtuelles) *Créer et gérer une instance avec Horizon*](/Cloud/IPC/Instances#horizon)
{.links-list}

Sélectionnez l'image `Debian 11.1 bullseye`:
![infomaniak-publiccloud_10_3.png](/images/cloud/infomaniak-public-cloud/10/infomaniak-publiccloud_10_3.png =600x)

Bien choisir la configuration adaptée à votre besoin : 
![infomaniak-publiccloud_8_3.png](/images/cloud/infomaniak-public-cloud/8/infomaniak-publiccloud_8_3.png =600x)

Et pensez à bien ajouter vos groupes de sécurités en conséquence : (ici pour le port HTTP (80), HTTPS (443), SSH (22) et Ping (ICMP))
![infomaniak-publiccloud_10_4.png](/images/cloud/infomaniak-public-cloud/10/infomaniak-publiccloud_10_4.png =600x)



## CLI
### Création du groupe de sécurité pour les ports HTTP et HTTPS
Voir ce tutoriel pour l'explication des commandes :
  - [🛡️ Les groupes de sécurité *Comprendre et utiliser le firewall et ses règles en CLI*](/Cloud/IPC/Security-Groups#cli)
{.links-list}

```bash
openstack security group create --description "HTTP (80) & HTTPS (443)" HTTP-HTTPS
openstack security group rule create --dst-port 80 --protocol TCP HTTP-HTTPS    
openstack security group rule create --dst-port 443 --protocol TCP HTTP-HTTPS    
```
### Création de l'instance
Voir ce tutoriel pour l'explication des commandes :
 - [⚡ Les instances (machines virtuelles) *Créer et gérer une instance avec Horizon*](/Cloud/IPC/Instances#cli)
{.links-list}

```bash
openstack server create --image "Debian 11.1 bullseye" --flavor a2-ram4-disk50-perf1 --security-group "PING - SSH" --security-group "HTTP-HTTPS" --key-name <KEYPAIR> --network ext-net1 Docker
openstack server show Docker
```

# Installation
## Connexion à l'instance
Dans un terminal, connectez vous avec :
```bash
ssh debian@<IP_INSTANCE>
```
> ***IP_INSTANCE***
> - Adresse IP de votre instance `Docker`
>
> 	.
{.is-info}

## Installation de Docker
### Mettez à jour le serveur :
```bash
sudo apt update && sudo apt upgrade
```
### Installez les sources de `Docker` :
```bash
apt install -y curl apt-transport-https ca-certificates gnupg2 software-properties-common apache2-utils
curl -fsSL https://download.docker.com/linux/$(. /etc/os-release; echo "$ID")/gpg | apt-key add -
add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/$(. /etc/os-release; echo "$ID") $(lsb_release -cs) stable"
apt-get update
```
### Installez `Docker` :
```bash
apt-get -y install docker-ce docker-compose
systemctl enable docker
systemctl start docker
```

## Installation de Traefik
### Créez un dossier `traefik` :
```bash
mkdir -p /apps/traefik
cd /apps/traefik
```
### Créez le premier fichier de configuration `traefik.yml`:
```yaml
api:
  dashboard: true
entryPoints:
  http:
    address: ":80"
  https:
    address: ":443"
providers:
  docker:
    endpoint: "unix:///var/run/docker.sock"
    exposedByDefault: false
certificatesResolvers:
  http:
    acme:
      email: $EMAIL
      storage: acme.json
      httpChallenge:
        entryPoint: http
providers.file:
    filename: "/etc/traefik/dynamic_conf.toml"
    watch: true
```
> Changez la variable `EMAIL` par votre adresse mail
{.is-warning}

### Créez le second fichier de configuration `config.yml`:
```yaml
http:
  middlewares:
    https-redirect:
      redirectScheme:
        scheme: https
    default-headers:
      headers:
        frameDeny: true
        sslRedirect: true
        browserXssFilter: true
        contentTypeNosniff: true
        forceSTSHeader: true
        stsIncludeSubdomains: true
        stsPreload: true
    secured:
      chain:
        middlewares:
        - default-headers
```
### Créez le fichier `acme.json` et configurer ses permissions :
```yaml
touch /apps/traefik/acme.json
chmod 600 /apps/traefik/acme.json
```

### Créez et copiez le mot de passe chiffré pour accéder à l'interface Traefik :
```bash
echo $(htpasswd -nb <USER> <PASSWORD>) | sed -e s/\\$/\\$\\$/g
```
> Changez les variables `USER` et `PASSWORD` par votre utilisateur et son mot de passe
{.is-warning}

> Voici un exemple de retour : `user:$$apr1$$vBPOdKH4$$mYgQp2CFIJcW3XX8wOOZr0`
{.is-info}

### Créez le fichier `docker-compose.yml` pour Traefik :
```yaml
version: '2'
services:
  traefik:
    image: traefik:latest
    container_name: traefik
    restart: unless-stopped
    security_opt:
      - no-new-privileges:true
    networks:
      - proxy
    ports:
      - 80:80
      - 443:443
    volumes:
      - /etc/localtime:/etc/localtime:ro
      - /var/run/docker.sock:/var/run/docker.sock:ro
      - /apps/traefik/traefik.yml:/traefik.yml:ro
      - /apps/traefik/acme.json:/acme.json
      - /apps/traefik/config.yml:/config.yml:ro
    labels:
      - traefik.enable=true
      - traefik.http.routers.traefik.entrypoints=http
      - traefik.http.routers.traefik.rule=Host("traefik.$NDD")
      - traefik.http.middlewares.traefik-auth.basicauth.users=$USERPASS
      - traefik.http.middlewares.traefik-https-redirect.redirectscheme.scheme=https
      - traefik.http.routers.traefik.middlewares=traefik-https-redirect
      - traefik.http.routers.traefik-secure.entrypoints=https
      - traefik.http.routers.traefik-secure.rule=Host("traefik.$NDD")
      - traefik.http.routers.traefik-secure.middlewares=traefik-auth
      - traefik.http.routers.traefik-secure.tls=true
      - traefik.http.routers.traefik-secure.tls.certresolver=http
      - traefik.http.routers.traefik-secure.service=api@internal
networks:
  proxy:
    external: true
```
> Changez la variable `USERPASS` par le mot de passe copié précédemment et la variable `NDD` par votre nom de domaine
{.is-warning}

### Créez le réseau `proxy` dans Docker :
```bash
docker network create proxy
```

### Lancez le docker-compose `traefik` :
```bash
docker-compose -f /apps/traefik/docker-compose.yml up -d
```

## Installation de Portainer
### Créez un dossier `portainer` :
```bash
mkdir -p /apps/portainer
cd /apps/portainer
```
### Créez le fichier `docker-compose.yml` pour Portainer :
```yaml
version: '2'
services:
  portainer:
    image: portainer/portainer-ce:latest
    container_name: portainer
    restart: unless-stopped
    security_opt:
      - no-new-privileges:true
    environment:
      TEMPLATES: https://github.com/PAPAMICA/docker-compose-collection/blob/master/templates-portainer.json
    networks:
      - proxy
    volumes:
      - /etc/localtime:/etc/localtime:ro
      - /var/run/docker.sock:/var/run/docker.sock:ro
      - /apps/portainer/data:/data
    labels:
      - traefik.enable=true
      - traefik.http.routers.portainer.entrypoints=http
      - traefik.http.routers.portainer.rule=Host("portainer.$NDD")
      - traefik.http.middlewares.portainer-https-redirect.redirectscheme.scheme=https
      - traefik.http.routers.portainer.middlewares=portainer-https-redirect
      - traefik.http.routers.portainer-secure.entrypoints=https
      - traefik.http.routers.portainer-secure.rule=Host("portainer.$NDD")
      - traefik.http.routers.portainer-secure.tls=true
      - traefik.http.routers.portainer-secure.tls.certresolver=http
      - traefik.http.routers.portainer-secure.service=portainer
      - traefik.http.services.portainer.loadbalancer.server.port=9000
      - traefik.docker.network=proxy
    
networks:
  proxy:
    external: true
```
> Changez la variable `NDD` par votre nom de domaine
{.is-warning}

### Lancez le docker-compose `portainer` :
```bash
docker-compose -f /apps/portainer/docker-compose.yml up -d
```

> L'installation est terminée !
{.is-success}