---
title: Infomaniak Public Cloud - Le stockage Swift
description: Créer et gérer ses container Swift et ses objects
published: true
date: 2021-11-04T08:20:50.110Z
tags: infomaniak, public-cloud, cloud, ipc, swift
editor: markdown
dateCreated: 2021-11-03T12:34:36.327Z
---

![plan_de_travail_1@4x.png](/images/cloud/infomaniak-public-cloud/plan_de_travail_1@4x.png =1000x)
# Introduction
## Stockage object
## Conteneur
## Object

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
> **--storage-policy** : Nom de la storage policy à appliquer au conteneur
> **--public** : Activer les liens publiques du conteneur
> **CONTAINER** : Conteneur à créer
{.is-info}

### Lister les conteneurs existants :
```bash
openstack container list
```
### Supprimer un conteneur :
```bash
openstack container delete [--recursive] <CONTAINER>
```
> **--recursive** : Supprimer récursivement les objets dans le conteneur avant la suppression du conteneur
> **CONTAINER** : Conteneur à supprimer
{.is-info}

# Télécharger un object
## Horizon
### Cliquez sur le bouton `Upload`, selectionnez votre fichier et renommer le si besoin
![infomaniak-publiccloud_2_4.png](/images/cloud/infomaniak-public-cloud/2/infomaniak-publiccloud_2_4.png =1300x)

## CLI
### Envoyer un fichier
```bash
openstack object create [--name <NAME>] <CONTAINER> <FILENAME>
```
> **--name <name>** : Téléchargez un fichier et renommez-le. Ne peut être utilisé que lors du téléchargement d'un seul objet
> **CONTAINER** : Conteneur pour le nouvel objet
> **FILENAME** : Nom du fichier local à télécharger
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
    openstack object create $CONTAINER $FILE
        if test $? -eq 0; then
            echo "[$(date +%Y-%m-%d_%H:%M:%S)]   SendFile   ✅   $FILE has been successfully sent to $CONTAINER."
        else
            echo "[$(date +%Y-%m-%d_%H:%M:%S)]   BackupScript   ❌   ERROR : A problem was encountered during the upload of $FILE"
        fi
done
```
Pour envoyer un dossier et ses sous dossiers :
```bash
./sendfolder.sh <CONTAINER> <FOLDER>
```
> **CONTAINER** : Conteneur pour les nouveaux objects
> **FOLDER** : Chemin du dossier local à télécharger
{.is-info}
  
### Lister les objects
```bash
openstack object list <CONTAINER>
```
> **CONTAINER** : Lister les objects de ce conteneur
{.is-info}
### Supprimer un fichier
```bash
openstack object delete <CONTAINER> <OBJECT>
```
> **CONTAINER** : Nom du container contenant l'object
> **OBJECT** : Nom de l'object à supprimer 
{.is-info}
  