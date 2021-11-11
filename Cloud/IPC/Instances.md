---
title: Infomaniak Public Cloud - Gestion des instances
description: Créer et gérer les instances
published: true
date: 2021-11-09T08:40:55.955Z
tags: infomaniak, public-cloud, cloud, ipc, vm, instance
editor: markdown
dateCreated: 2021-11-04T12:38:36.261Z
---

![plan_de_travail_1@4x.png](/images/cloud/infomaniak-public-cloud/plan_de_travail_1@4x.png =600x)

# Introduction
Une instance est une machine virtuelle fonctionnant sur le Public Cloud d'Infomaniak.
# Horizon
## Créer une instance
### Rendez vous dans la section 'Instance' et cliquez sur `Lancer une instance`
![infomaniak-publiccloud_5_1.png](/images/cloud/infomaniak-public-cloud/5/infomaniak-publiccloud_5_1.png =1300x)

### Nommez votre instance, choisissez le nombre et cliquez sur `suivant`
![infomaniak-publiccloud_5_2.png](/images/cloud/infomaniak-public-cloud/5/infomaniak-publiccloud_5_2.png =600x)

### Selectionnez l'image et cliquez sur `Suivant`
![infomaniak-publiccloud_5_3.png](/images/cloud/infomaniak-public-cloud/5/infomaniak-publiccloud_5_3.png =600x)

### Choisisez la configuration souhaitée et cliquez sur `Suivant`
![infomaniak-publiccloud_5_4.png](/images/cloud/infomaniak-public-cloud/5/infomaniak-publiccloud_5_4.png =600x)


### Puis le réseau et cliquez sur `Suivant`
![infomaniak-publiccloud_5_5.png](/images/cloud/infomaniak-public-cloud/5/infomaniak-publiccloud_5_5.png =600x)


### Ajoutez votre groupe de sécurité et cliquez sur `Lancer l'instance`
![infomaniak-publiccloud_5_6v2.png](/images/cloud/infomaniak-public-cloud/5/infomaniak-publiccloud_5_6v2.png =600x)
> Vous ne pourrez pas vous y connecter en SSH si vous ne configurez pas un groupe de sécurité ! Voir ce tutoriel : 
{.is-warning}
 - [🛡️ Les groupes de sécurité *Comprendre et utiliser le firewall et ses règles*](/Cloud/IPC/Security-Groups)
{.links-list}

> Votre instance est démarrée !
> ![infomaniak-publiccloud_5_7.png](/images/cloud/infomaniak-public-cloud/5/infomaniak-publiccloud_5_7.png =1300x)
{.is-success}



# CLI
## Documentation OpenStack
 - [🔗 OpenStack Docs : server *Documentation officielle*](https://docs.openstack.org/python-openstackclient/xena/cli/command-objects/server.html)
{.links-list}
## Lister les configurations disponibles
```bash
openstack flavor list
```
## Lister les images disponibles
```bash
openstack image list
```
## Lister les réseaux disponibles
```bash
openstack network list
```
## Créer une instance
```bash
openstack server create
    (--image <IMAGE> | --volume <VOLUME>)
    --flavor <FLAVOR>
    [--security-group <SECURITY_GROUP>]
    [--key-name <KEY_NAME>]
    [--network <NETWORK>]
    <SERVER_NAME>
```
> **--image *IMAGE***
> - Créer un disque de démarrage du serveur à partir de cette image (nom ou ID)
>
> **--volume *VOLUME***
> - Créez un serveur en utilisant ce volume comme disque de démarrage (nom ou ID).
>
> **--flavor *FLAVOR***
> - Créer un serveur avec cette configuration (nom ou ID)
>
> **--security-group *SECURITY_GROUP***
> - Groupe de sécurité à attribuer à ce serveur (nom ou ID) (répétez l'option pour définir plusieurs groupes)
>
> **--key-name *KEY_NAME***
> - Paire de clés à injecter dans ce serveur 
>
> **--network *NETWORK***
> - Créez une carte réseau sur le serveur et connectez-la au réseau. (nom ou ID)
>
> ***SERVER_NAME***
> - Nom du serveur à créer
>
> 	.
{.is-info}

## Lister les instances
```bash
openstack server list
    [--name <NAME_REGEX>]
    [--instance-name <SERVER_NAME>]
    [--status <STATUS>]
    [--flavor <FLAVOR>]
    [--image <IMAGE>]
```
> **--name *NAME_REGEX***
> - Filtrer par nom (avec une regex)
>
> **--instance-name *SERVER_NAME***
> - Filtrer par nom d'instance
>
> **--status *STATUS***
> - Filtrer par status
>
> **--flavor *FLAVOR***
> - Filtrer par configuration
>
> **--image *IMAGE***
> - Filtrer par image
>
> 	.
{.is-info}

## Afficher une instance
```bash
openstack server show <INSTANCE>
```
> ***INSTANCE***
> - Nom ou ID de l'instance à afficher
>
> 	.
{.is-info}

## Supprimer une instance
```bash
openstack server delete <INSTANCE>
```
> ***INSTANCE***
> - Nom ou ID de l'instance à supprimer
>
> 	.
{.is-info}

# Connexion en SSH à l'instance
Si votre groupe de sécurité autorise le port SSH, vous pouvez vous connecter comme ceci :
```bash
ssh <USER>@<IP>
```
## Connaitre l'identifiant de votre instance
L'identifiant par défaut est propre à chaque image, voici une petite liste non exaustive :
| Distribution | Utilisateur par défaut |
| --- | --- |
| Debian | `debian` |
| Ubuntu | `ubuntu` |
| Gento | `cloud` |
> Si vous ne connaissez pas l'identifiant, essayez de vous connecter en `root`
{.is-info}

## Connaitre l'adresse IP de votre instance
### Horizon
![infomaniak-publiccloud_6_6.png](/images/cloud/infomaniak-public-cloud/6/infomaniak-publiccloud_6_6.png =1300x)

### CLI
```bash
openstack server show <INSTANCE>
```
> ***INSTANCE***
> - Nom ou ID de l'instance à afficher
>
> 	.
{.is-info}
#### Résultat
L'adresse IP se trouve sur la ligne `adresses` :
```bash
+-----------------------------+-------------------------------------------------------------+
| Field                       | Value                                                       |
+-----------------------------+-------------------------------------------------------------+
| OS-DCF:diskConfig           | AUTO                                                        |
| OS-EXT-AZ:availability_zone | dc3-a-04                                                    |
| OS-EXT-STS:power_state      | Running                                                     |
| OS-EXT-STS:task_state       | None                                                        |
| OS-EXT-STS:vm_state         | active                                                      |
| OS-SRV-USG:launched_at      | 2021-11-05T09:21:53.000000                                  |
| OS-SRV-USG:terminated_at    | None                                                        |
| accessIPv4                  |                                                             |
| accessIPv6                  |                                                             |
| addresses                   | ext-net1=195.15.240.XX, 2001:1600:10:100::xxx               |
| config_drive                |                                                             |
| created                     | 2021-11-05T09:21:38Z                                        |
| flavor                      | a2-ram4-disk20-perf1 (b6b7baeb-2328-48c9-8543-88cccec8ec4b) |
| hostId                      | aa89adc55bcd3e134e2286f254c31e38a34a9219191293ba4xxxxxxx    |
| id                          | 5dfd8567-2d92-4db9-8896-ca4a8xxxxxxx                        |
| image                       | Debian 11.1 bullseye (3fd029f9-144f-4206-a845-b71166a4d6ad) |
| key_name                    | PAPAMICA-INFOKEY                                            |
| name                        | Wiki-Tech-Lab1                                              |
| progress                    | 0                                                           |
| project_id                  | 7368f02b559648d0a9ff15bfxxxxxxxx                            |
| properties                  |                                                             |
| security_groups             | name='default'                                              |
|                             | name='PING - SSH'                                           |
| status                      | ACTIVE                                                      |
| updated                     | 2021-11-05T09:21:53Z                                        |
| user_id                     | 0d2d91edd8bf4b7297ef3b6a6xxxxxxx                            |
| volumes_attached            |                                                             |
+-----------------------------+-------------------------------------------------------------+
```