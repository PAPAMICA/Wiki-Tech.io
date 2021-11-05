---
title: Infomaniak Public Cloud - Créer une image personnalisée
description: Créer une image à partir d'une instance et démarrer depuis cette image
published: true
date: 2021-11-05T13:02:37.358Z
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
## Horizon
### Créer un volume d'une instance
### Démarrer une instance sur ce volume
## CLI
### Créer un volume d'une instance
### Démarrer une instance sur ce volume
### Lister les volumes
### Afficher un volume
### Supprimer un volume