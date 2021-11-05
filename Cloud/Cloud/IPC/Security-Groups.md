---
title: Infomaniak Public Cloud - Groupes de sécurité
description: Gérer le firewall des ressources (ouverture de ports etc...)
published: true
date: 2021-11-05T08:42:27.428Z
tags: openstack, public-cloud, cloud, ipc, firewall
editor: markdown
dateCreated: 2021-11-05T08:42:27.428Z
---

![plan_de_travail_1@4x.png](/images/cloud/infomaniak-public-cloud/plan_de_travail_1@4x.png =600x)
# Introduction
Les groupes de sécurité sont des « firewall » pour vos ressources. Ils possèdent plusieurs caractéristiques :
- Les groupes de sécurité sont **STATEFUL** par défaut
- **Il n’est pas possible** d’utiliser les groupes de sécurité pour **bloquer une adresse IP** ou **un port spécifique**
- Il n’est pas possible de créer des règles de blocage seulement des règles d’autorisation

# Horizon
## Créer un groupe de sécurité
### Rendez vous dans la section `Groupes de Sécurité` sous `Réseaux` et cliquez sur `+ Créer un groupe de Sécurité`
![infomaniak-publiccloud_6_1.png](/images/cloud/infomaniak-public-cloud/6/infomaniak-publiccloud_6_1.png =1300x)
### Nommez votre groupe de sécurité et cliquez sur `Créer un Groupe de Sécurité`
![infomaniak-publiccloud_6_2.png](/images/cloud/infomaniak-public-cloud/6/infomaniak-publiccloud_6_2.png =600x)
## Créer une règle pour SSH (22)
### Cliquez sur `Ajouter une règle`
![infomaniak-publiccloud_6_3.png](/images/cloud/infomaniak-public-cloud/6/infomaniak-publiccloud_6_3.png =1300x)
### Selectionnez le protocol, le(s) port(s) et son scope d'action puis cliquez sur `Ajouter`
![infomaniak-publiccloud_6_4.png](/images/cloud/infomaniak-public-cloud/6/infomaniak-publiccloud_6_4.png =600x)

> Cette procédure est valable pour tous les ports TCP et UDP
{.is-info}


## Créer une règle pour PING (ICMP)
### Cliquez sur `Ajouter une règle`
![infomaniak-publiccloud_6_3.png](/images/cloud/infomaniak-public-cloud/6/infomaniak-publiccloud_6_3.png =1300x)
### Selectionnez le protocol `ALL ICMP` et son scope d'action puis cliquez sur `Ajouter`
![infomaniak-publiccloud_6_5.png](/images/cloud/infomaniak-public-cloud/6/infomaniak-publiccloud_6_5.png =600x)

# CLI
## Documentation OpenStack
 - [🔗 OpenStack Docs : security group *Documentation officielle*](https://docs.openstack.org/python-openstackclient/xena/cli/command-objects/security-group.html)
{.links-list}
## Créer un groupe de sécurité
```bash 
openstack security group create [--description <DESCRIPTION>] <NAME>
```
## Créer une règle pour SSH (22)
## Créer une règle pour PING (ICMP)
## Lister les groupes de sécurités
## Supprimer un groupe de sécurité