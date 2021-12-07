---
title: Infomaniak Public Cloud - Mise en situation : Uptime Kuma
description: Mettre en place sa premiere application
published: true
date: 2021-12-07T17:14:32.304Z
tags: openstack, infomaniak, public-cloud, cloud, ipc
editor: markdown
dateCreated: 2021-12-07T17:10:21.300Z
---

![plan_de_travail_1@4x.png](/images/cloud/infomaniak-public-cloud/plan_de_travail_1@4x.png =600x)

# Introduction
Pour notre première mise en situation, mettons en place une petite application simple mais utile : Uptime Kuma !
Uptime Kuma est une alternative OpenSource et SelfHosted d'Uptime Robot. Elle se veux simple à mettre en place, à administrer et à utiliser. Elle dispose d'un large panel de check et de notifications.

Démo : [uptime.papamica.com](https://uptime.papamica.com)
Github : [louislam/uptime-kuma](https://github.com/louislam/uptime-kuma)

# Template Heat
## Créez le fichier `uptime-kuma.yml`:
```yaml
heat_template_version: rocky
description:  Uptime Kuma
parameters:
  image:
    type: string
    description: image use for kuma
    default: Debian 11.1 bullseye
  flavor:
    type: string
    description: flavor used by kuma
    default: a1-ram2-disk20-perf1
  network:
    type: string
    description: network used by kuma
    default: kuma-network
  subnet_id:
    type: string
    description: dedicated subnet for kuma
    default: kuma-subnet
  floating_network_id:
    type: string
    description: UUID of a Neutron external network
    default: ext-floating1

resources:
  wait_condition:
    type: OS::Heat::WaitCondition
    properties:
      handle: { get_resource: wait_handle }
      timeout: 600

  wait_handle:
    type: OS::Heat::WaitConditionHandle
  


  # security group
  kuma_security_group:
    type: OS::Neutron::SecurityGroup
    properties:
      name: "kuma_security_group"
      description: >
        Allows 3001
      rules:
         - { direction: ingress, protocol: tcp, port_range_min: 3001, port_range_max: 3001 }

  # network resources
  kuma_network:
    type: OS::Neutron::Net
    properties:
      name: { get_param: network }
      value_specs:
        mtu: 1500

  kuma_subnet:
    type: OS::Neutron::Subnet
    properties:
      name: 'kuma-subnet'
      network_id: { get_resource: kuma_network }
      cidr: "10.11.3.0/24"
      dns_nameservers:
        - "84.16.67.69"
        - "84.16.67.70"
      ip_version: 4

  kuma_router:
    type: OS::Neutron::Router
    properties:
      name:  'kuma-router'
      external_gateway_info: { network: ext-floating1 }

  kuma_router_subnet_interface:
    type: OS::Neutron::RouterInterface
    properties:
      router_id: { get_resource: kuma_router }
      subnet: { get_resource: kuma_subnet }

  kuma_port:
    type: OS::Neutron::Port
    properties:
      network: { get_resource: kuma_network }
      security_groups: [ { get_resource: kuma_security_group } ]
      fixed_ips:
        - subnet_id: { get_resource: kuma_subnet }

  kuma_floating:
    type: OS::Neutron::FloatingIP
    properties:
      floating_network_id: { get_param: floating_network_id }
      port_id: { get_resource: kuma_port }

  # instance
  server:
    type: OS::Nova::Server
    depends_on: [ kuma_router]
    properties:
      flavor: { get_param: flavor }
      image: { get_param: image }
      networks:
        - port: { get_resource: kuma_port }
      user_data:
        str_replace:
          template: |
            #!/bin/bash -v
            apt update && apt upgrade -y
            apt install -y git curl
            curl -sL https://deb.nodesource.com/setup_16.x | sudo bash -
            apt update
            apt install -y nodejs
            npm install pm2 -g
            git clone https://github.com/louislam/uptime-kuma.git
            cd uptime-kuma
            npm run setup
            pm2 start server/server.js --name uptime-kuma
            pm2 startup
            wc_notify --data-binary '{"status": "SUCCESS"}'

          params:
            wc_notify: { get_attr: ['wait_handle', 'curl_cli'] }


outputs:
  server_IP:
    value: 
      str_replace:
        template: http://IP:3001
        params:
          IP: { get_attr: [kuma_floating, floating_ip_address] }
    description: "IP"
```

## Déployer la stack
Lancez la création de la stack avec la commande suivante :
```bash
openstack stack create --template uptime-kuma.yml uptime-kuma
```

Après quelques minutes, vous pouvez utilisez la commande suivante pour afficher l'IP de votre Uptime Kuma :
```bash
openstack stack show uptime-kuma

+-----------------------+----------------------------------------------------------------------------------------------------------------------------------+
| Field                 | Value                                                                                                                            |
+-----------------------+----------------------------------------------------------------------------------------------------------------------------------+
| id                    | 65277898-f5e5-495c-9010-c84eff43c656                                                                                             |
| stack_name            | uptime                                                                                                                           |
| description           | Uptime Kuma                                                                                                                      |
| creation_time         | 2021-11-25T10:29:33Z                                                                                                             |
| updated_time          | None                                                                                                                             |
| stack_status          | CREATE_COMPLETE                                                                                                                  |
| stack_status_reason   | Stack CREATE completed successfully                                                                                              |
| parameters            | OS::project_id: 7368f02b559648d0a9ff15bff29b464f                                                                                 |
|                       | OS::stack_id: 65277898-f5e5-495c-9010-c84eff43c656                                                                               |
|                       | OS::stack_name: uptime-kuma                                                                                                      |
|                       | flavor: a1-ram2-disk20-perf1                                                                                                     |
|                       | floating_network_id: ext-floating1                                                                                               |
|                       | image: Debian 11.1 bullseye                                                                                                      |
|                       | network: kuma-network                                                                                                            |
|                       | subnet_id: kuma-subnet                                                                                                           |
|                       |                                                                                                                                  |
| outputs               | - description: IP                                                                                                                |
|                       |   output_key: server_IP                                                                                                          |
|                       |   output_value: http://195.15.246.154:3001                                                                                       |
|                       |                                                                                                                                  |
| links                 | - href: https://api.pub1.infomaniak.cloud/v1/7368f02b559648d0a9ff15bff29b464f/stacks/uptime/65277898-f5e5-495c-9010-c84eff43c656 |
|                       |   rel: self                                                                                                                      |
|                       |                                                                                                                                  |
| deletion_time         | None                                                                                                                             |
| notification_topics   | []                                                                                                                               |
| capabilities          | []                                                                                                                               |
| disable_rollback      | True                                                                                                                             |
| timeout_mins          | None                                                                                                                             |
| stack_owner           | PCU-U2CAZJ4                                                                                                                      |
| parent                | None                                                                                                                             |
| stack_user_project_id | 14890fd555b648049c0da38ae51e6b97                                                                                                 |
| tags                  | []                                                                                                                               |
|                       |                                                                                                                                  |
+-----------------------+----------------------------------------------------------------------------------------------------------------------------------+
```

Dans notre exemple, Uptime Kuma est accessible depuis : `http://195.15.246.154:3001`
