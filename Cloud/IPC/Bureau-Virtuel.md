---
title: Infomaniak Public Cloud - Mise en situation : Bureau virtuel
description: Mettre en place un bureau virtuel avec Ubuntu
published: true
date: 2021-11-12T09:16:16.952Z
tags: openstack, infomaniak, public-cloud, cloud, ipc, vdi, ubuntu
editor: markdown
dateCreated: 2021-11-08T16:33:28.599Z
---

![plan_de_travail_1@4x.png](/images/cloud/infomaniak-public-cloud/plan_de_travail_1@4x.png =600x)

# Introduction
Vous pouvez très bien utiliser un "ordinateur dans le cloud" avec une interface graphique pour certaines tâches basiques ou avancées. Nous verrons ici l'installation d'un serveur Ubuntu averc une interface graphique (Gnome) et la connexion à ce dernier en RDP directement.
Vous pouvez utilisez le **template Heat** directement ou faire son installation à la main.
![infomaniak-publiccloud_8_5.png](/images/cloud/infomaniak-public-cloud/8/infomaniak-publiccloud_8_5.png =1300x)

# Template Heat
## Créez le fichier `desktop.yml`:
```yaml
heat_template_version: rocky
description:  Desktop Ubuntu
parameters:
  image:
    type: string
    description: image use for desktop
    default: Ubuntu 20.04 LTS Focal Fossa
  key:
    type: string
    description: SSH key to connect to the servers
  flavor:
    type: string
    description: flavor used by desktop
    default: a4-ram8-disk50-perf1
  network:
    type: string
    description: network used by desktop
    default: desktop-network
  subnet_id:
    type: string
    description: dedicated subnet for desktop
    default: desktop-subnet
  floating_network_id:
    type: string
    description: UUID of a Neutron external network
    default: ext-floating1
  USER_NAME:
    type: string
    description: Name of user
    default: infomaniak
  USER_PASSWORD:
    type: string
    description: Password of user
    default: PublicCloud
  KEYBOARD:
    type: string
    description: Kayboard layout (en / fr)
    default: fr

resources:
  wait_condition:
    type: OS::Heat::WaitCondition
    properties:
      handle: { get_resource: wait_handle }
      timeout: 600

  wait_handle:
    type: OS::Heat::WaitConditionHandle


  # security group
  desktop_security_group:
    type: OS::Neutron::SecurityGroup
    properties:
      name: "desktop_security_group"
      description: >
        Allows ICMP, SSH & RDP default port
      rules:
         - { direction: ingress, protocol: icmp }
         - { direction: ingress, protocol: tcp, port_range_min: 22, port_range_max: 22 }
         - { direction: ingress, protocol: tcp, port_range_min: 3389, port_range_max: 3389 }

  # network resources
  desktop_network:
    type: OS::Neutron::Net
    properties:
      name: { get_param: network }
      value_specs:
        mtu: 1500

  desktop_subnet:
    type: OS::Neutron::Subnet
    properties:
      name: 'desktop-subnet'
      network_id: { get_resource: desktop_network }
      cidr: "10.11.3.0/24"
      dns_nameservers:
        - "84.16.67.69"
        - "84.16.67.70"
      ip_version: 4

  desktop_router:
    type: OS::Neutron::Router
    properties:
      name:  'desktop-router'
      external_gateway_info: { network: ext-floating1 }

  desktop_router_subnet_interface:
    type: OS::Neutron::RouterInterface
    properties:
      router_id: { get_resource: desktop_router }
      subnet: { get_resource: desktop_subnet }

  desktop_port:
    type: OS::Neutron::Port
    properties:
      network: { get_resource: desktop_network }
      security_groups: [ { get_resource: desktop_security_group } ]
      fixed_ips:
        - subnet_id: { get_resource: desktop_subnet }

  desktop_floating:
    type: OS::Neutron::FloatingIP
    properties:
      floating_network_id: { get_param: floating_network_id }
      port_id: { get_resource: desktop_port }

  # instance
  server:
    type: OS::Nova::Server
    depends_on: [ desktop_router]
    properties:
      flavor: { get_param: flavor }
      image: { get_param: image }
      key_name: {get_param: key}
      networks:
        - port: { get_resource: desktop_port }
      user_data:
        str_replace:
          template: |
            #!/bin/bash -v
            apt update
            apt install -y tasksel
            tasksel install ubuntu-desktop
            apt upgrade -y
            apt install -y xrdp
            PASS=$(perl -e 'print crypt("$USER_PASSWORD", "salt"),"\n"')
            useradd -m -p $PASS $USER_NAME
            usermod -aG sudo $USER_NAME
            KEYBOARD='$KEYBOARD' && sudo sed -i 's/XKBLAYOUT=\"\w*"/XKBLAYOUT=\"'$KEYBOARD'\"/g' /etc/default/keyboard
            echo "deb [ arch=amd64 ] https://repo.linit.io linit-stable main 2" > /etc/apt/sources.list.d/linit.list
            wget -qO - https://repo.linit.io/public.key | sudo apt-key add -
            apt update
            apt install -y kdrive kmeet
            rm /etc/default/locale
            cat <<EOF >>/etc/default/locale
            LANG="fr_FR.UTF-8"
            LANGUAGE="fr"

            LC_NUMERIC="fr_FR.UTF-8"
            LC_TIME="fr_FR.UTF-8"
            LC_MONETARY="fr_FR.UTF-8"
            LC_PAPER="fr_FR.UTF-8"
            LC_IDENTIFICATION="fr_FR.UTF-8"
            LC_NAME="fr_FR.UTF-8"
            LC_ADDRESS="fr_FR.UTF-8"
            LC_TELEPHONE="fr_FR.UTF-8"
            LC_MEASUREMENT="fr_FR.UTF-8"
            EOF
            wc_notify --data-binary '{"status": "SUCCESS"}'
            reboot

          params:
            wc_notify: { get_attr: ['wait_handle', 'curl_cli'] }
            $KEYBOARD: { get_param: KEYBOARD }
            $USER_PASSWORD: { get_param: USER_PASSWORD }
            $USER_NAME: { get_param: USER_NAME }


outputs:
  desktop_url:
    value:
      str_replace:
        template: host:3389
        params:
          host: { get_attr: [desktop_floating, floating_ip_address] }
    description: "RDP IP"

  desktop_password:
    value: { get_param: USER_PASSWORD }
    description: "User password"

  desktop_login:
    value: { get_param: USER_NAME }
    description: "User"
```
## Déployer la stack
Lancez la création de la stack avec la commande suivante :
```bash
openstack stack create --template desktop.yml desktop --parameter key=<KEY> --parameter KEYBOARD=<KEYBOARD> --parameter USER_NAME=<USER> --parameter USER_PASSWORD='<PASSWORD>' 
```


> ***KEY***
> - Nom ou ID de votre clé SSH
>
> ***KEYBOARD***
> - Configuration de votre clavier (`fr` / `en` / `es`) (Défaut : `fr`)
>
> ***USER***
> - Nom en miniscule de votre utilisateur final pour se connecter (Défaut : `infomaniak`)
>
> ***PASSWORD***
> - Mot de passe de votre utilisateur final (Défaut : `PublicCloud`)
>
> 	.
{.is-info}

Pour la gestion de la stack, voir ce tutoriel :
  - [🔥 L'orchestration *Créer et gérer les stacks Heat*](/Cloud/IPC/Orchestration)
{.links-list}

# Création de la VM
## Horizon
### Création du groupe de sécurité pour le port RDP
Pour nous connecter à la machine virtuelle une fois installée, nous allons avoir besoin d'ouvrir le port RDP (`3389`). Si vous avez bien suivis les autres tutoriel, cela devrais etre simple. Créez donc un nouveau groupe de sécurité `RDP`en suivant ce tutoriel :

  - [🛡️ Les groupes de sécurité *Comprendre et utiliser le firewall et ses règles avec Horizon*](/Cloud/IPC/Security-Groups#horizon)
{.links-list}

Et ajoutez la règle suivante : 
![infomaniak-publiccloud_8_1.png](/images/cloud/infomaniak-public-cloud/8/infomaniak-publiccloud_8_1.png =600x)

### Création de l'instance
Comme pour la création du groupe de sécurité, utilisez le tutoriel suivant pour la création de l'instance : 
 - [⚡ Les instances (machines virtuelles) *Créer et gérer une instance avec Horizon*](/Cloud/IPC/Instances#horizon)
{.links-list}

Sélectionnez l'image `Ubuntu 20,04 LTS Focal Fossa`:
![infomaniak-publiccloud_8_2.png](/images/cloud/infomaniak-public-cloud/8/infomaniak-publiccloud_8_2.png =600x)

Bien choisir la configuration adaptée à votre besoin : 
![infomaniak-publiccloud_8_3.png](/images/cloud/infomaniak-public-cloud/8/infomaniak-publiccloud_8_3.png =600x)

Et pensez à bien ajouter vos groupes de sécurités en conséquence : (ici pour le port RDP (3389), SSH (22) et Ping (ICMP))
![infomaniak-publiccloud_8_4.png](/images/cloud/infomaniak-public-cloud/8/infomaniak-publiccloud_8_4.png =600x)



## CLI
### Création du groupe de sécurité pour le port RDP
Voir ce tutoriel pour l'explication des commandes :
  - [🛡️ Les groupes de sécurité *Comprendre et utiliser le firewall et ses règles en CLI*](/Cloud/IPC/Security-Groups#cli)
{.links-list}

```bash
openstack security group create --description "RDP (3389)" RDP
openstack security group rule create --dst-port 3389 --protocol TCP RDP    
```
### Création de l'instance
Voir ce tutoriel pour l'explication des commandes :
 - [⚡ Les instances (machines virtuelles) *Créer et gérer une instance avec Horizon*](/Cloud/IPC/Instances#cli)
{.links-list}

```bash
openstack server create --image "Ubuntu 20.04 LTS Focal Fossa" --flavor a2-ram4-disk50-perf1 --security-group "PING - SSH" --security-group "RDP" --key-name <KEYPAIR> --network ext-net1 Ubuntu-Desktop
openstack server show Ubuntu-Desktop
```

# Installation
## Connexion à l'instance
Dans un terminal, connectez vous avec :
```bash
ssh ubuntu@<IP_INSTANCE>
```
> ***IP_INSTANCE***
> - Adresse IP de votre instance `Ubuntu-Desktop`
>
> 	.
{.is-info}

## Création de l'utilisateur
### Commençons par créer l'utilisateur avec lequel vous allez vous connecter en RDP :
```bash
PASS=$(perl -e 'print crypt("<USER_PASSWORD>", "salt"),"\n"')
sudo useradd -m -p $PASS <USER>
```
> ***USER_PASSWORD***
> - Mot de passe de votre utilisateur
>
> ***USER***
> - Nom de votre utilisateur **en miniscule**
>
> 	.
{.is-info}

### Et ajoutez cet utilisateur au groupe `sudo`:
```bash
sudo usermod -aG sudo <USER>
```
> ***USER***
> - Nom de votre utilisateur **en miniscule**
>
> 	.
{.is-info}

## Installation de l'interface graphique
### Mettez à jour le serveur :
```bash
sudo apt update && sudo apt upgrade
```
### Installez `tasksel`et lancez l'installation de gnome :
```bash
sudo apt install -y tasksel
sudo tasksel install ubuntu-desktop
```
## Installation de `xrdp`
### Il ne vous reste plus qu'à installer `xrdp` et redémarrer le server :
```bash
sudo apt install xrdp
sudo reboot
```

# Connexion en RDP
## Linux
Connectez vous avec le l'application [**Remmina**](https://remmina.org/)
## MacOS
Connectez vous avec l'application [**Microsoft Remote Desktop**](https://apps.apple.com/us/app/microsoft-remote-desktop/id1295203466?mt=12)
## Windows
Connectez vous avec l'outils de connexion RDP [**MSTSC**](/Microsoft/Windows-7-10/RDP)