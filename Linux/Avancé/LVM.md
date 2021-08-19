---
title: Linux - LVM
description: Comprendre et utiliser la gestion des disques logiques.
published: true
date: 2021-06-14T07:56:07.914Z
tags: 
editor: markdown
dateCreated: 2021-05-24T10:35:20.029Z
---

![Accéder à un volume LVM | M'sieur Duhaz](https://data.duhaz.fr/media/uploads/sshot4d33ca58bce04.png)

# Introduction

La gestion logique des volumes fournit une vision de plus haut-niveau de l'espace disque sur un ordinateur que la vue traditionnelle des disques et des partitions. Cela offre à l'administrateur plus de souplesse pour allouer du stockage aux applications ou aux utilisateurs.

Les volumes de stockage créés sous le contrôle du gestionnaire logiques de volumes (LVM) peuvent être redimensionnés et déplacés presque à volonté.

Il existe plusieurs types :

-   **PV** : Volume Physique. Les disques durs, partitions de disques durs, volumes RAID ou unités logiques provenant d'un SAN forment des "volumes physiques" (physical volumes ou PV).
-   **VG** : Groupe de Volumes. C'est un assemblage d'un ou plusieurs Volumes Physiques (PV).
-   **LV** : Volume logique. Les LV redécoupent les Groupes de Volumes sous forme de partition virtuelle.
-   **PE** : pour être manipuler, les données sont divisées en blocs de données appelé extensions physiques (Physical Extents).
-   **LE** : même chose que pour les PE mais au niveau logique (Logical Extent). La taille des blocs est la même dans pour chaque volume logique (LV) d'un même groupe de volume (VG).
{.grid-list}

Avant toute chose, il faut installer le paquet requis : `apt install lvm2`

# Création de disques logiques

## Création des disques physiques (PV)

> Rappel : les volumes physiques sont les disques durs physiques, RAID, ... Cette commande va rendre les volumes disponibles pour la création du LVM.
{.is-info}

*Syntaxe :*

```bash
pvcreate /dev/disque
```

Création :

```bash
pvcreate /dev/sdX
pvcreate /dev/sdY
```

> Il y a deux possibilités sur la création des disques physiques:
> 1.  On prend tout le disque
> 2.  On crée une partition dédiée au LVM
{.is-info}

Nous l'aurez compris, il faut répéter cette commande pour **tous les disques durs** devant être mis dans le cluster LVM.  
On vérifie ensuite avec la commande `pvdisplay`.

## Création d'un groupe de volume (VG)

> Rappel : Un groupe de volume va permettre de rassembler plusieurs volumes **physiques** pour n'en faire qu'un. *Par exemple, pour 3 disques dur de 1To, on peut créer un groupe sur ces 3 disques, puis créer une partition de 3To si on en a besoin.*
{.is-info}

*Syntaxe :*

```bash
vgcreate nomGroupe /dev/disque1 /dev/disque2 /dev/disqueX
```

*Création d'un groupe* `*test*` *avec les disques sda et sdb :*

```bash
vgcreate test /dev/sda /dev/sdb
```

On vérifie avec la commande `vgdisplay`

## Création des volumes logiques (LV)

*Syntaxe :*

```bash
lvcreate -n nomVolume -L taille<m/g> nomGroupe
```

*Création de deux volumes logiques : sauvegarde et données :*

```bash
lvcreate -n sauvegarde -L 50g test
lvcreate -n données -L 500g test
```

### Création des systèmes de fichier

*Syntaxe :*

```bash
mkfs -t <type> /dev/nomGroupe/nomVolume # On formatte la partition
mkdir /chemin/montage # On crée le chemin pour recevoir le montage
mount /dev/nomGroupe/nomVolume /chemin/montage # On monte la partition dans répertoire
```

*Exemple :*

```bash
mkfs -t ext4 /dev/test/sauvegarde
mkdir /mnt/sauvegarde
mount /dev/test/sauvegarde /mnt/sauvegarde
```

# Manipuler ses disques logiques

## Groupe de volume

### Agrandir un groupe de volume

1.  Création d'un volume physique (disque à ajouter) :

```bash
pvcreate /dev/sdX
```

1.  Ajouter le volume physique dans le groupe de volume :

```bash
vgextend nomGroupe /dev/sdX
```

1.  On étend le volume logique (*optionnel, peut être utiliser pour en créer un nouveau au lieu d'en agrandir un.*)

```bash
lvresize -L +taille<m/g> /dev/nomGroupe/nomVolume # Redimentionner le volume
resize2fs /dev/nomGroupe/nomVolume # Redimentionner le système de fichier
```

## Volumes logiques

### Agrandir un volume logique

1.  Démonter les systèmes de fichier concernés : `umount nomDuMontage`
2.  Augmenter la taille du volume logique : `lvresize --size +<taille>G /dev/<nom_groupe_vol>/<vol>`

> Pour l'étape suivante, ne pas oublier de mettre la taille à la fin de la commande, sinon le système se mettra en erreur.
{.is-warning}

1.  Augmenter la taille du système de fichier : `resize2fs /dev/<nom_groupe_vol>/<vol> TailleFinale<M/G>`
2.  Vérifier d'éventuelles erreurs sur le système de fichier : `e2fsck -ff /dev/<nom_groupe_vol>/<vol>`

### Réduire un volume logique

> Si la taille de **destination** du volume est inférieure à la taille **utilisée**, alors il y a un risque de perte de données. Bien que les outils peuvent vous prévenir si c'est le cas, ce n'est pas obligatoire et il faut rester vigilant.
{.is-warning}

1.  Démonter le volume concerné : `umount <point-de-montage>`
2.  Vérifier d'éventuelles erreurs sur le système de fichier : `e2fsck -ff /dev/<nom_groupe_vol>/<vol>`

> Pour l'étape suivante, ne pas oublier de mettre la taille à la fin de la commande, sinon le système se mettra en erreur.
{.is-warning}

1.  Réduire la taille du système de fichier : `resize2fs /dev/<nom_groupe_vol>/<vol> Taille<M/G>`
2.  Réduire la taille du volume logique : `lvreduce -L -<taille>G /dev/<nom_groupe_vol>/<vol>`

> La taille ici correspond à la différence entre la taille initiale et la taille d'arrivée. Le calcul sera donc **taille\_initiale-taille\_finale**.
{.is-info}

1.  Redimentionner le système de fichier (préventif) : `resize2fs /dev/<nom_groupe_vol>/<vol>`
2.  Remonter le système de fichier.

Source : [Persold.ovh](https://wiki.persold.ovh)