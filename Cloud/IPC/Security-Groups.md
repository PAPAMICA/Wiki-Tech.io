---
title: Infomaniak Public Cloud - Groupes de sécurité
description: Gérer le firewall des ressources (ouverture de ports etc...)
published: true
date: 2021-11-05T09:16:05.944Z
tags: openstack, public-cloud, cloud, ipc, firewall
editor: markdown
dateCreated: 2021-11-05T08:42:27.428Z
---

![plan_de_travail_1@4x.png](/images/cloud/infomaniak-public-cloud/plan_de_travail_1@4x.png =600x)
# Introduction
Les groupes de sécurité sont des « firewalls » pour vos ressources. Ils possèdent plusieurs caractéristiques :
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
### Selectionnez le protocole, le(s) port(s) et son scope d'action puis cliquez sur `Ajouter`
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
  - [🔗 OpenStack Docs : security group rule *Documentation officielle*](https://docs.openstack.org/python-openstackclient/xena/cli/command-objects/security-group-rule.html)
{.links-list}
## Créer un groupe de sécurité
```bash 
openstack security group create [--description <DESCRIPTION>] <NAME>
```
> **--description *DESCRIPTION***
> - Description pour le groupe de sécurité à créer
>
> ***NAME***
> - Nom du groupe de sécurité à créer
>
> 	.
{.is-info}
## Créer une règle
```bash
openstack security group rule create
    [--remote-ip <IP_ADDRESS> | --remote-group <GROUP>]
    [--dst-port <PORT_RANGE>]
    [--protocol <PROTOCOL>]
    [--description <DESCRIPTION>]
    [--icmp-type <ICMP_TYPE>]
    [--icmp-code <ICMP_CODE>]
    [--ingress | --egress]
    [--ethertype <ETHERTYPE>]
    <GROUP>
```
> **--remote-ip *IP_ADDRESS***
> - Bloc d'adresse IP distante (Utilisez la notation CIDR : valeur par défaut pour la règle IPv4 : `0.0.0.0/0`, valeur par défaut pour la règle IPv6 : `::/0`)
>
> **--remote-group *GROUP***
> - Nom ou ID du groupe de sécurité distant
>
> **--dst-port *PORT_RANGE***
> - Le port de destination peut être un port unique ou une plage de ports : `137:139`. Requis pour les protocoles IP TCP et UDP.
>
> **--protocol *PROTOCOL***
> - Protocole à utiliser (ICMP / TCP / UPD)
>
> **--description *DESCRIPTION***
> - Définir la description de la règle du groupe de sécurité
>
> **--icmp-type *ICMP_TYPE***
> - Type ICMP pour les protocoles IP ICMP
>
> **--icmp-code *ICMP_CODE***
> - Code ICMP pour les protocoles IP ICMP
>
> **--ingress | --egress**
> - Appliquer la règle au trafic entrant ou sortant
>
> **--ethertype *ETHERTYPE***
> - Ethertype de trafic réseau (IPv4, IPv6 ; par défaut : basé sur le protocole IP)
>
> ***GROUP***
> - Créer une règle dans ce groupe de sécurité (nom ou ID)
>
> 	.
{.is-info}
### Exemple 1 : SSH (22)
```bash
openstack security group rule create --remote-ip 0.0.0.0/0 --dst-port 22 --protocol TCP --description SSH --ingress <GROUP>
```
### Exemple 2 : PING (ICMP)
```bash
openstack security group rule create --remote-ip 0.0.0.0/0 --protocol ICMP --description PING --ingress <GROUP>
```
## Lister les rèles
```bash
openstack security group rule list
    [--protocol <PROTOCOL>]
    [--ingress | --egress]
    [<GROUP>]
```
> **--protocol *PROTOCOL***
> - Filtrer par protocole (ICMP / TCP / UDP)
>
> **--ingress | --egress**
> - Filtrer par trafic entrant ou sortant
>
> ***GROUP***
> - Filtrer par groupe de sécurité
>
> 	.
{.is-info}
## Afficher une règles
```bash
openstack security group rule show <RULE>
```
> ***RULE***
> - Nom ou ID de la règle à afficher
>
> 	.
{.is-info}
## Supprimer une règle
```bash
openstack security group rule delete <RULE>
```
> ***RULE***
> - Nom ou ID de la règle à supprimer
>
> 	.
{.is-info}
## Lister les groupes de sécurités
```bash
openstack security group list
```
## Afficher un groupe de sécurité
```bash
openstack security group show <GROUP>
```
> ***GROUP***
> - Nom ou ID du groupe de sécurité à afficher
>
> 	.
{.is-info}
## Lister un groupe de sécurité
```bash
openstack security group list
```
## Supprimer un groupe de sécurité
```bash
openstack security group delete <GROUP>
```
> ***GROUP***
> - Nom ou ID du groupe de sécurité à supprimer
>
> 	.
{.is-info}