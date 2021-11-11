---
title: Infomaniak Public Cloud - Les volumes
description: Créer et gérer les volumes (stockage bloc)
published: true
date: 2021-11-09T11:45:44.537Z
tags: openstack, infomaniak, public-cloud, cloud, ipc, volume, ceph
editor: markdown
dateCreated: 2021-11-09T09:28:55.603Z
---

![plan_de_travail_1@4x.png](/images/cloud/infomaniak-public-cloud/plan_de_travail_1@4x.png =600x)

# Introduction
Les volumes Openstack sont du **stockage block** qui utilise le **service Cinder** qui repose sur différents backends (Ceph, LVM, etc...)
Les volumes permettent d'**ajouter un "disque dur" supplémentaire** à une instance.
Il ne peux etre attaché qu'à **une seule instance à la fois**.
Il peux être **déplacer** d'une instance à une autre.
Pour être exploité, il dois être **formaté et monté** dans le système d'exploitation de l'instance.

# Horizon
## Créer un volume
### Rendez vous dans la catégorie `Volumes` et cliquez sur `Créer un Volume`
![infomaniak-publiccloud_9_1.png](/images/cloud/infomaniak-public-cloud/9/infomaniak-publiccloud_9_1.png =1300x)
### Nommez votre volume, selectionnez la taille et cliquez sur `Créer un Volume`
![infomaniak-publiccloud_9_2.png](/images/cloud/infomaniak-public-cloud/9/infomaniak-publiccloud_9_2.png =600x)


## Connecter un volume à une instance
### Rendez vous dans la catégorie `Instances`, choisissez votre instance et cliquez sur `Attacher un Volume`
![infomaniak-publiccloud_9_3.png](/images/cloud/infomaniak-public-cloud/9/infomaniak-publiccloud_9_3.png =1300x)

### Selectionnez votre volume et cliquez sur `Attacher un Volume`
![infomaniak-publiccloud_9_4.png](/images/cloud/infomaniak-public-cloud/9/infomaniak-publiccloud_9_4.png =600x)


# CLI
## Documentation OpenStack
  - [🔗 OpenStack Docs : volume *Documentation officielle*](https://docs.openstack.org/python-openstackclient/xena/cli/command-objects/volume.html)
{.links-list}
## Créer un volume
```bash
openstack volume create
    [--size <SIZE>]
    [--type <volume-type>]
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

## Attacher un volume à une instance
```bash
openstack server add volume <INSTANCE> <VOLUME> --device <CHEMIN>
```
> ***INSTANCE***
> - Nom ou ID de l'instance
>
> ***VOLUME***
> - Nom ou ID du volume
>
> **--device *CHEMIN***
> - Chemin du périphérique (exemple : `/dev/vdb`)
>
> 	.
{.is-info}
## Détacher un volume à une instance
```bash
openstack server remove volume <INSTANCE> <VOLUME>
```
> ***INSTANCE***
> - Nom ou ID de l'instance
>
> ***VOLUME***
> - Nom ou ID du volume
>
> 	.
{.is-info}
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

# Utiliser le volume
Avant de pouvoir utiliser ce volume attaché à l'instant, il faut d'abord le formater puis le monter. Ici nous utiliserons un exemple avec Debian.

Vous pouvez suivre ce tutoriel suivant :
  - [💾 Ajouter un disque ou un volume *Formater et monter un disque ou un volume*](/Linux/Débutant/Ajout-Disque-Volume)
{.links-list}