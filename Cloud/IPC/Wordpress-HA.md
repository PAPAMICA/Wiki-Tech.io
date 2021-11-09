---
title: Infomaniak Public Cloud - Mise en situation : VDI Ubuntu
description: Mettre en place un bureau virtuel avec Ubuntu*
published: true
date: 2021-11-09T07:49:29.570Z
tags: openstack, infomaniak, public-cloud, cloud, ipc, vdi, ubuntu
editor: markdown
dateCreated: 2021-11-08T16:33:28.599Z
---

![plan_de_travail_1@4x.png](/images/cloud/infomaniak-public-cloud/plan_de_travail_1@4x.png =600x)

# Introduction

# Template Heat
### Créez le fichier `vdi.yml`:
```yaml
heat_template_version: rocky
description:  VDI Ubuntu
parameters:
  image:
    type: string
    description: image use for vdi
    default: Ubuntu 20.04 LTS Focal Fossa
  key:
    type: string
    description: SSH key to connect to the servers
  flavor:
    type: string
    description: flavor used by vdi
    default: a4-ram8-disk50-perf1
  network:
    type: string
    description: network used by vdi
    default: vdi-network
  subnet_id:
    type: string
    description: dedicated subnet for vdi
    default: vdi-subnet
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
  vdi_security_group:
    type: OS::Neutron::SecurityGroup
    properties:
      name: "vdi_security_group"
      description: >
        Allows ICMP, SSH & RDP default port
      rules:
         - { direction: ingress, protocol: icmp }
         - { direction: ingress, protocol: tcp, port_range_min: 22, port_range_max: 22 }
         - { direction: ingress, protocol: tcp, port_range_min: 3389, port_range_max: 3389 }

  # network resources
  vdi_network:
    type: OS::Neutron::Net
    properties:
      name: { get_param: network }
      value_specs:
        mtu: 1500

  vdi_subnet:
    type: OS::Neutron::Subnet
    properties:
      name: 'vdi-subnet'
      network_id: { get_resource: vdi_network }
      cidr: "10.11.3.0/24"
      dns_nameservers:
        - "84.16.67.69"
        - "84.16.67.70"
      ip_version: 4

  vdi_router:
    type: OS::Neutron::Router
    properties:
      name:  'vdi-router'
      external_gateway_info: { network: ext-floating1 }

  vdi_router_subnet_interface:
    type: OS::Neutron::RouterInterface
    properties:
      router_id: { get_resource: vdi_router }
      subnet: { get_resource: vdi_subnet }

  vdi_port:
    type: OS::Neutron::Port
    properties:
      network: { get_resource: vdi_network }
      security_groups: [ { get_resource: vdi_security_group } ]
      fixed_ips:
        - subnet_id: { get_resource: vdi_subnet }

  vdi_floating:
    type: OS::Neutron::FloatingIP
    properties:
      floating_network_id: { get_param: floating_network_id }
      port_id: { get_resource: vdi_port }

  # instance
  server:
    type: OS::Nova::Server
    depends_on: [ vdi_router]
    properties:
      flavor: { get_param: flavor }
      image: { get_param: image }
      key_name: {get_param: key}
      networks:
        - port: { get_resource: vdi_port }
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
  vdi_url:
    value:
      str_replace:
        template: host:3389
        params:
          host: { get_attr: [vdi_floating, floating_ip_address] }
    description: "RDP IP"

  vdi_password:
    value: { get_param: USER_PASSWORD }
    description: "User password"

  vdi_login:
    value: { get_param: USER_NAME }
    description: "User"
```

Lancez la création de la stack avec la commande suivante :
```bash
openstack stack create --template vdi.yml vdi --parameter KEY=<KEY> --parameter KEYBOARD=<KEYBOARD> --parameter USER_NAME=<USER> --parameter USER_PASSWORD='<PASSWORD>' 
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
### Création de l'instance

## CLI
### Création du groupe de sécurité pour le port RDP
### Création de l'instance

# Installation
## Connexion à l'instance
## Création de l'utilisateur
## Installation de l'interface graphique
## Installation de `xrdp`

# Connexion en RDP
## Linux
## MacOS
## Windows