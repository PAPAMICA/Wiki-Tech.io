---
title: Infomaniak Public Cloud - Créer une image personnalisée
description: Créer une image à partir d'une instance et démarrer depuis cette image
published: true
date: 2021-11-05T13:32:49.208Z
tags: openstack, infomaniak, public-cloud, cloud, ipc
editor: markdown
dateCreated: 2021-11-05T11:04:22.782Z
---

![plan_de_travail_1@4x.png](/images/cloud/infomaniak-public-cloud/plan_de_travail_1@4x.png =600x)
# Introduction
Il existe deux manières de créer une image d'une instance pour l'utiliser sur une autre instance : soit par un **snapshot**, soit par un **volume**.
## Snapshot 
Le snapshot est très **utile pour sauvegarder** un état d'une instance ou pour la dupliquer.
- L'image n'est jamais altérée (réutilisable).
- Plusieurs instances peuvent utiliser cette image.
- Très peu coûteux (stockage objet). 
## Volume
Le volume sert principalement à la **migration d'instance**.
- Plus rapide à provisionner.
- Une seule instance sur un volume.
- Coûte plus cher (stockage block).

# Utiliser un snapshot
## Horizon
### Créer un snapshot d'une instance
#### Rendez vous dans la section `Instances`, selectionnez l'instance que vous souhaitez et cliquer sur `Créer un Snapshot`
![infomaniak-publiccloud_7_1.png](/images/cloud/infomaniak-public-cloud/7/infomaniak-publiccloud_7_1.png =1300x)
#### Nommez le et cliquez sur `Créer le Snapshot`
![infomaniak-publiccloud_7_2.png](/images/cloud/infomaniak-public-cloud/7/infomaniak-publiccloud_7_2.png =600x)

> Au bout de quelques minutes, votre snapshot est disponible !
> ![infomaniak-publiccloud_7_3.png](/images/cloud/infomaniak-public-cloud/7/infomaniak-publiccloud_7_3.png =1300x)
{.is-success}


### Démarrer une instance sur ce snapshot
#### Durant la création de l'instance, selectionnez la source `Instance Snapshot` et choisissez votre snapshot
![infomaniak-publiccloud_7_4.png](/images/cloud/infomaniak-public-cloud/7/infomaniak-publiccloud_7_4.png =1300x)

## CLI
### Documentation OpenStack
  - [🔗 OpenStack Docs : server image *Documentation officielle*](https://docs.openstack.org/python-openstackclient/xena/cli/command-objects/server-image.html)
{.links-list}
### Créer un snapshot d'une instance
```bash
openstack server image create
    [--name <IMAGE_NAME>]
    <SERVER>
```
> **--name *IMAGE_NAME***
> - Nom de la nouvelle image disque (par défaut : nom du serveur)
>
> ***SERVER***
> - Nom ou ID du serveur pour créer l'image
>
> 	.
{.is-info}

### Démarrer une instance sur ce snapshot
Utilisez le nom de l'image ou son ID avec ce tutoriel :
- [⚡ Les instances (machines virtuelles) *Créer et gérer une instance*](https://wiki-tech.io/Cloud/IPC/Instances#cr%C3%A9er-une-instance-1)
{.links-list}

# Utiliser un volume
Les volumes sont créés à partir d'un snapshot, il faut donc faire un snapshot de votre instance avant de le transformer en volume.
## Horizon
### Créer un volume d'une instance
#### Selectionnez votre snapshot et cliquez sur `Créer un volume`
![infomaniak-publiccloud_7_6.png](/images/cloud/infomaniak-public-cloud/7/infomaniak-publiccloud_7_6.png =1300x)

#### Nommez le et choisissez la taille puis cliquez sur `Créer un volume`
![infomaniak-publiccloud_7_7.png](/images/cloud/infomaniak-public-cloud/7/infomaniak-publiccloud_7_7.png =600x)

> Après quelques minutes, votre volume est disponible !
> ![infomaniak-publiccloud_7_8.png](/images/cloud/infomaniak-public-cloud/7/infomaniak-publiccloud_7_8.png =1300x)
{.is-success}

### Démarrer une instance sur ce volume
#### Durant la création de l'instance, selectionnez la source `Volume` et choisissez votre volume
![infomaniak-publiccloud_7_9.png](/images/cloud/infomaniak-public-cloud/7/infomaniak-publiccloud_7_9.png =600x)

## CLI
### Documentation OpenStack
  - [🔗 OpenStack Docs : volume *Documentation officielle*](https://docs.openstack.org/python-openstackclient/xena/cli/command-objects/volume.html)
{.links-list}
### Créer un volume d'une instance
```bash
openstack volume create
    [--size <SIZE>]
    [--type <VOLUME_TYPE>]
    [--image <IMAGE> | --snapshot <SNAPSHOT> | --source <VOLUME> ]
    [--description <DESCRIPTION>]
    [--bootable | --non-bootable]
    [--read-only | --read-write]
    <NAME>
```
> **--size *SIZE***
> - Taille du volume en Go (obligatoire sauf si –snapshot ou –source est spécifié)
>
> **--type *VOLUME_TYPE***
> - Définir le type de volume parmis les types disponibles (`openstack volume type list`)
>
> **--image *IMAGE* | --snapshot *SNAPSHOT* | --source *VOLUME***
> - Utiliser une des sources avec son nom ou ID
>
> **--description *DESCRIPTION***
> - Modifier la description du volume
>
> **--bootable | --non-bootable**
> - Rendre le volume bootable ou non (par défaut : `non-bootable`)
>
> **---read-only | --read-write**
> - Régler les autorisation en lecture/écriture (par défaut : `read-write`)
>
> ***NAME***
> - Nom du volume à créer
>
> 	.
{.is-info}

### Démarrer une instance sur ce volume
Utilisez l'option `--volume`et le nom du volume ou son ID avec ce tutoriel :
- [⚡ Les instances (machines virtuelles) *Créer et gérer une instance*](https://wiki-tech.io/Cloud/IPC/Instances#cr%C3%A9er-une-instance-1)
{.links-list}
### Lister les volumes
```bash
openstack volume list
```
### Afficher un volume
```bash
openstack volume show <VOLUME>
```
> ***VOLUME***
> - Nom ou ID du volume à afficher
>
> 	.
{.is-info}
### Supprimer un volume
```bash
openstack volume delete <VOLUME>
```
> ***VOLUME***
> - Nom ou ID du volume à supprimer
>
> 	.
{.is-info}