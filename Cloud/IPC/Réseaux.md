---
title: Infomaniak Public Cloud - Les réseaux
description: Créer et gérer les réseaux
published: true
date: 2021-11-09T13:48:54.561Z
tags: openstack, réseau, infomaniak, public-cloud, cloud, ipc
editor: markdown
dateCreated: 2021-11-09T13:48:54.561Z
---

![plan_de_travail_1@4x.png](/images/cloud/infomaniak-public-cloud/plan_de_travail_1@4x.png =600x)

# Introduction
Le Cloud Public d'Infomaniak met à disposision 3 réseaux principaux :
- **Réseau externe** (***ext-net1*** & ***ext-v6only1***) : Permet d’avoir une instance directement connectée à internet
- **Réseau floating** (***ext-floating1***) : Permet de réserver une adresse IP publique et de l’attribuer dynamiquement à des ressources
- 



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