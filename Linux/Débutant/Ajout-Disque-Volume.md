---
title: Linux Débutant - Ajouter un disque ou un volume
description: Formater et monter un disoue ou un volume
published: true
date: 2021-11-09T11:41:07.773Z
tags: linux, disque, volume, formater, monter
editor: markdown
dateCreated: 2021-11-09T11:41:05.887Z
---

![linux.png](/images/linux/linux.png =600x)
# Introduction
Avant de pouvoir utiliser un nouveau disque ou un volume sous Linux, il faut le formater et le monter à l'endroit voulu sur le système.

# Lister les disques / volumes
## Commandes
```bash
lsblk -f
```
## Exemple
```bash
$ lsblk -f
NAME    FSTYPE FSVER LABEL UUID                                 FSAVAIL FSUSE% MOUNTPOINT
sda
├─sda1  ext4   1.0         bf7e597b-61f0-48c6-a5e6-8723108ff10e   17.9G     4% /
├─sda14
└─sda15 vfat   FAT16       DB4B-2471                             117.8M     5% /boot/efi
sdb     ext4   1.0         f5f2667d-35e1-4bdf-9a18-2186454145fd
```
# Formater un disque / volume
## Commandes
```bash
mkfs [-t <TYPE> ] <DEVICE> [<SIZE>]
```
> **-t *TYPE***
> - Type de partition (`ext4` / `vfat` / `ntfs`) 
>
> ***DEVICE***
> - Chemin de disque ou du volume (`/dev/xxxXX`)
>
> ***SIZE***
> - Taille de la partition (optionnel)
>
> 	.
{.is-info}
## Exemple
```bash
$ sudo mkfs -t ext4 /dev/sdb

$ lsblk -f
NAME    FSTYPE FSVER LABEL UUID                                 FSAVAIL FSUSE% MOUNTPOINT
sda
├─sda1  ext4   1.0         bf7e597b-61f0-48c6-a5e6-8723108ff10e   17.9G     4% /
├─sda14
└─sda15 vfat   FAT16       DB4B-2471                             117.8M     5% /boot/efi
sdb     ext4   1.0         f5f2667d-35e1-4bdf-9a18-2186454145fd
```

# Monter un disque / volume
## Commandes
### Créer le dossier où monter le disque / volume 
```bash
sudo mkdir -p <CHEMIN>
```
> ***CHEMIN***
> - Chemin du dossier à créer
>
> 	.
{.is-info}
### Monter le disque / volume
```bash
sudo mount -t auto <DEVICE> <CHEMIN>
```
> ***DEVICE***
> - Chemin de disque ou du volume (`/dev/xxxXX`)
>
> ***CHEMIN***
> - Chemin du dossier précédemment créer
>
> 	.
{.is-info}

## Exemple
```bash
$ sudo mkdir -p /mnt/test

$ sudo mount /dev/sdb /mnt/test

$ lsblk -f
NAME    FSTYPE FSVER LABEL UUID                                 FSAVAIL FSUSE% MOUNTPOINT
sda
├─sda1  ext4   1.0         bf7e597b-61f0-48c6-a5e6-8723108ff10e   17.9G     4% /
├─sda14
└─sda15 vfat   FAT16       DB4B-2471                             117.8M     5% /boot/efi
sdb     ext4   1.0         f5f2667d-35e1-4bdf-9a18-2186454145fd    9.2G     0% /mnt/test
```

> Votre disque / volume est désormais accessible depuis votre système d'exploitation !
{.is-success}
