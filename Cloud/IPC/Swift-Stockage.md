---
title: Infomaniak Public Cloud - Swift : Le stockage object
description: Créer et gérer ses container Swift et ses objects
published: true
date: 2021-11-04T11:08:09.713Z
tags: infomaniak, public-cloud, cloud, ipc, swift
editor: markdown
dateCreated: 2021-11-03T12:34:36.327Z
---

![plan_de_travail_1@4x.png](/images/cloud/infomaniak-public-cloud/plan_de_travail_1@4x.png =600x)
# Introduction
## Stockage objet
Appelé également stockage basé sur les objets, le stockage d’objets est un terme général faisant référence à la façon dont nous organisons et travaillons avec les unités de stockage, appelées objets.

## Objet
Dans un système de stockage de type objet, un objet est tout simplement un fichier stocké dans le dit système. En revanche il se caractérise par trois éléments qui sont propres à ce type de stockage
- Les données elles-mêmes 
- Les métadonnées qui contiennent des informations contextuelles sur ce que sont les données, leurs finalités, leur confidentialité ou toute autre information pertinente pour le traitement des informations
- Un identificateur unique à l’échelle mondiale. Il s’agit d’une adresse donnée à l’objet afin qu’il puisse être retrouvé sur un système réparti

## Conteneur
Un conteneur Swift est un espace de stockage sur lequel on peut appliquer une politique de stockage (uniquement à la création) et un accès public ou privé. C'est lui qui va contenir les objects envoyés sur Swift.

# Créer un conteneur
## Horizon
### Rendez vous dans la catégorie `Conteneurs` sous `Stockage d'objet` dans votre dashboard Horizon et cliquez sur `+ Conteneur`
![infomaniak-publiccloud_2_1.png](/images/cloud/infomaniak-public-cloud/2/infomaniak-publiccloud_2_1.png =1300x)

### Nommez votre container, choisissez sa Storage Policy et s'il est publique ou non
![infomaniak-publiccloud_2_2.png](/images/cloud/infomaniak-public-cloud/2/infomaniak-publiccloud_2_2.png =1300x)

> Votre conteneur est créé !
{.is-success}

### Voici les différentes fonctionnalités disponibles sur le dashboard Horizon :
1. Informations à propos du conteneur (Activation ou desactivation du mode `Accès publique` et suppression du conteneur)
2. Rechercher des objects dans le conteneur
3. Télécharger un object vers le conteneur
4. Créer un "Dossier"
5. Supprimer un object

![infomaniak-publiccloud_2_3.png](/images/cloud/infomaniak-public-cloud/2/infomaniak-publiccloud_2_3.png =1300x)

## CLI
### La création d'un conteneur se fait avec la commande suivante :
```bash
openstack container create [--storage-policy <POLICY_NAME>] [--public] <CONTAINER>
```
> **--storage-policy**
> - Nom de la storage policy à appliquer au conteneur
>
> **--public**
> - Activer les liens publics du conteneur
>
> **CONTAINER**
> - Conteneur à créer
>
> 	.
{.is-info}

### Lister les conteneurs existants :
```bash
openstack container list
```
### Supprimer un conteneur :
```bash
openstack container delete [--recursive] <CONTAINER>
```
> **--recursive**
> - Supprimer récursivement les objets dans le conteneur avant la suppression du conteneur
>
> **CONTAINER**
> - Conteneur à supprimer
>
> 	.
{.is-info}

# Télécharger un objet
## Horizon
### Cliquez sur le bouton `Upload`, selectionnez votre fichier et renommer le si besoin
![infomaniak-publiccloud_2_4.png](/images/cloud/infomaniak-public-cloud/2/infomaniak-publiccloud_2_4.png =1300x)

## CLI
### Envoyer un fichier
```bash
openstack object create [--name <NAME>] <CONTAINER> <FILENAME>
```
> **--name <name>**
> - Téléchargez un fichier et renommez-le. Ne peut être utilisé que lors du téléchargement d'un seul objet
>
> **CONTAINER**
> - Conteneur pour le nouvel objet
>
> **FILENAME**
> - Nom du fichier local à télécharger
>
> 	.
{.is-info}


### Envoyer un dossier
Pour envoyer plusieurs fichiers ou dossiers, vous pouvez utiliser un petit script comme celui ci :
```bash
#!/bin/bash

CONTAINER="$1"
FOLDER="$2"

FILESLIST=$(find $FOLDER -type f )
for FILE in $FILESLIST 
    do
    openstack object create $CONTAINER $FILE > /dev/null
        if test $? -eq 0; then
            echo "[$(date +%Y-%m-%d_%H:%M:%S)]   SendFolder   ✅   $FILE has been successfully sent to $CONTAINER."
        else
            echo "[$(date +%Y-%m-%d_%H:%M:%S)]   SendFolder   ❌   ERROR : A problem was encountered during the upload of $FILE"
        fi
done
```
Pour envoyer un dossier et ses sous dossiers :
```bash
./sendfolder.sh <CONTAINER> <FOLDER>
```
> **CONTAINER**
> - Conteneur pour les nouveaux objects
>
> **FOLDER**
> - Chemin du dossier local à télécharger
>
> 	.
{.is-info}
  
### Lister les objets
```bash
openstack object list <CONTAINER>
```
> **CONTAINER**
> - Lister les objets de ce conteneur
>
> 	.
{.is-info}
### Supprimer un fichier
```bash
openstack object delete <CONTAINER> <OBJECT>
```
> **CONTAINER**
> - Nom du container contenant l'objet
>
> **OBJECT**
> - Nom de l'objet à supprimer 
>
> 	.
{.is-info}
  