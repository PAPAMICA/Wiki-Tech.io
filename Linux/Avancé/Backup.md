---
title: Backup
description: Faire ses sauvegardes sous Linux
published: true
date: 2021-06-14T07:44:04.611Z
tags: 
editor: markdown
dateCreated: 2021-05-24T10:35:12.754Z
---

![Linux server backup and replication services | Dogsbody Technology](https://www.dogsbody.com/wp-content/uploads/Server-backup.png)

# Sauvegarder son poste

## Dossier à sauvegarder

## Timeshift

## rsync

# Sauvegarder le disque d’un serveur

Depuis plusieurs années j’utilise des VPS chez différents hébergeurs, certains ont des systèmes de backups intéressants, d’autres non. C’est pour cela que je me suis mis en quête d’un système de sauvegarde qui fonctionnerais à chaque fois.

## Prérequis :

-   Un VPS avec un mode Rescue sous Debian
-   Un serveur FTP

## Lancer la sauvegarde :

Redémarrez votre VPS sur le disque de rescue. (via le panel de votre hébergeur)

Connectez vous en root.

Identifier la partition (ou le disque) que vous aller sauvegarder avec :fdisk -l

Installer ftp et dcfldd :

```plaintext
apt install ftp dcfldd
```

Connectez vous à votre serveur FTP :

```plaintext
ftp <IP_DU_SERVEUR>
```

Entrez votre identifiant et votre mot de passe.

Rendez vous dans le dossier où vous souhaitez faire votre sauvegarde.

Lancer la sauvegarde avec la commande suivante :put « |dcfldd if=/dev/MA\_PARTITION bs=32k sizeprobe=if statusinterval=5 | gzip » MON\_IMAGE.gz

La sauvegarde est en cours, soyez patient !

## Restaurer la sauvegarde :

Redémarrez votre VPS sur le disque de rescue. (via le panel de votre hébergeur)

Connectez vous en root.

Identifier la partition (ou le disque) que vous aller restaurer avec :

```plaintext
fdisk -l
```

Installer ftp et dcfldd :

```plaintext
apt install ftp dcfldd
```

Connectez vous à votre serveur FTP :

```plaintext
ftp IP_DU_SERVEUR
```

Entrez votre identifiant et votre mot de passe.

Rendez vous dans le dossier où vous avez stocké votre sauvegarde.

Lancer la restauration avec la commande suivante :

```plaintext
get MON_IMAGE.gz "| gunzip | dcfldd of=/dev/MA_PARTITION sizeprobe=of statusinterval=5"
```

La restauration est en cours, soyez patient !

Redémarrer votre VPS sur le disque principal.

# Sauvegarder avec Tar

## Utilisation Standard

Pour sauvegarder son système on peut utiliser la commande `tar` avec les paramètres suivants :

```plaintext
tar cvpzf backup-$(date +%d-%m-%Y).tar.gz --exclude=/backup-$(date +%d-%m-%Y).tar.gz --exclude=/proc --exclude=/tmp --exclude=/mnt --exclude=/dev --exclude=/sys --exclude=/run --exclude=/media --exclude=/var/log --exclude=/usr/src/linux-headers\* --exclude=/home /
```

> **Attention :** Cette commande est à lancer à la racine.

Ce la va créer à la racine un fichier backup.tar.gz avec la date d’aujourd’hui tout en excluant les dossiers temporaires et inutiles pour la restauration.

> Vous pouvez ajouter autant de répertoires à exclure de la sauvegarde que vous voulez avec le paramètre `--exclude`. En revanche, dans tout les cas il est inutile de prendre`/proc`, `/tmp`, `/dev` ,`/sys` et `/run`

## Avec Nohup

On peut aussi lancer la commander avec `nohup` pour éviter de perdre le processus une fois le terminal coupé (éviter l’envoi signal SIGHUP alias « *signal hang up* » qui clôture l’ensemble des processus enfants lancées par le terminal virtuel) :

```plaintext
nohup tar cvpzf backup-$(date +%d-%m-%Y).tar.gz --exclude=/backup-$(date +%d-%m-%Y).tar.gz --exclude=/proc --exclude=/tmp --exclude=/mnt --exclude=/dev --exclude=/sys --exclude=/run --exclude=/media --exclude=/var/log --exclude=/usr/src/linux-headers\* --exclude=/home --exclude=nohup.out / &
```

> Remarquez la présence `&` à la fin de la commande. Cela mets le processus en arrière plan (Que vous pouvez retrouver avec la commande `jobs`). 

> Vous pouvez suivre l’avancée de la sauvegarde listant le contenu de **nohup.out** :`tail -f nohup.out`

> **Attention :** Bien exclure le fichier **nohup.out** de la sauvegarde. Celui-ci est crée dans le répertoire où vous travaillez (En utilisant la variable **$PWD**).

# Sauvegarder avec Swiss Backup

# Sauvegarder un serveur avec Active Backup de Synology

## Présentation

## Configuration de la sauvegarde

# Sauvegarder avec BorgBackup

## Présentation 

Borg offre plusieurs avantages : 

-   Déduplication
-   Incrémental
-   Différentiel
-   Compression
-   Chiffrement

Documentation : [https://borgbackup.readthedocs.io/en/stable/](https://borgbackup.readthedocs.io/en/stable/ ) 

## Configuration

###   
Lexique : 

-   Dépôt : espace de stockage contenant les archives
-   Archive : un ou plusieurs dossiers/fichiers sauvegardés
-   Chunk : Les fichiers sont coupés en bloc de taille fixe avec un hash unique
-   srv-backup : Serveur de stockage hebergant les dépôts
-   srv-app : Serveur à Sauvegarder

### Installation du paquet

```plaintext
root@srv-backup:~# apt install borgbackup
```

```plaintext
root@srv-app:~# apt install borgbackup
```

### Configuration de srv-backup

```plaintext
root@srv-backup:~# adduser borg
borg@srv-backup:~$ mkdir ~/sauvegardes
```

### Setup SSH

```plaintext
root@srv-app:~$ ssh-keygen
root@srv-app:~$ ssh-copy-id borg@srv-backup
```

### Initialisation du dépôt

```plaintext
root@srv-app:~# borg init -e keyfile borg@srv-backup:sauvegardes/srv-app
```

### Première sauvegarde

```plaintext
root@srv-app:~# borg create borg@srv-backup:sauvegardes/srv-app::test /home
```

Vous pouvez ajouter des options, veuillez-vous référer à la documentation. Voici un exemple pour ajouter des informations pendant le transfert, choisir la compression lz4 et nommer l'archive avec la date du jour :

```plaintext
# https://borgbackup.readthedocs.io/en/stable/quickstart.html#backup-compression
# lz4 (super fast, low compression)
# zstd (wide range from high speed and low compression to high compression and lower speed)
# zlib (medium speed and compression)
# lzma (low speed, high compression)

root@srv-app:~# borg create --progress --stats --compression lz4 borg@srv-backup:sauvegardes/srv-app::{now:%Y-%m-%d} /home
```

### Information sur le dépôt et les archives

Information sur la taille du dépôt :

```plaintext
root@srv-app:~# borg info borg@srv-backup:sauvegardes/srv-app
```

Liste des archives du dépôt :

```plaintext
root@srv-app:~# borg list borg@srv-backup:sauvegardes/srv-app
```

Information sur la taille de l'archive :

```plaintext
root@srv-app:~# borg info borg@srv-backup:sauvegardes/srv-app::test
```

Liste des fichiers de l'archive :

```plaintext
root@srv-app:~# borg list borg@srv-backup:sauvegardes/srv-app::test
```

On peut voir le gains de place avec la commande borg info grâce à la compression et la déduplication :

```plaintext
                       Original size      Compressed size    Deduplicated size
All archives:                1.01 GB            444.37 MB            432.50 MB

                       Unique chunks         Total chunks
Chunk index:                   43183                44850
```

### Emplacement de la Config et du Cache

```plaintext
~/.config/borg
~/.cache/borg
```

Le dossier cache peut prendre de la place, vous pouvez le déplacer puis créer un lien symbolique pour éviter de saturer le filesystem par défaut.

### Monter une archive

```plaintext
root@srv-app:~# borg mount borg@srv-backup:sauvegardes/srv-app::test /mnt
```

### Supprimer une archive et un dépôt 

```plaintext
root@srv-app:~# borg delete borg@srv-backup:sauvegardes/srv-app::test
```

```plaintext
root@srv-app:~# borg delete borg@srv-backup:sauvegardes/srv-app
```

### Vérifier et Réparer

```plaintext
root@srv-app:~# borg check -v --progress borg@srv-backup:sauvegardes/srv-app::test
```

```plaintext
root@srv-app:~# borg check --repair borg@srv-backup:sauvegardes/srv-app::test
```

### Automatiser la sauvegarde

Voici un script d'exemple qui va sauvegarder /etc et /home puis faire une purge des archives :

```plaintext
#!/bin/bash

# Variables
set -e
export BORG_PASSPHRASE="`cat /path/passphrase`"
BORG_CLIENT=srv-app
BORG_REPO=borg@srv-backup:sauvegardes/${BORG_CLIENT}
BORG_LOG=/var/log/${BORG_CLIENT}.log
BORG_TARGET=(
/etc
/home
)

# Creation de l'archive 
borg create --exclude-caches --exclude-from /path/exclude_list --compression zstd ${BORG_REPO}::{now:%Y-%m-%d} "${BORG_TARGET[@]}" >> ${BORG_LOG} 2>&1

# Nettoyage des anciens backups
# - une archive par jour les 7 derniers jours,
# - une archive par semaine pour les 4 dernières semaines,
# - une archive par mois pour les 12 derniers mois.

borg prune ${BORG_REPO} --keep-daily=7 --keep-weekly=4 --keep-monthly=12 >> ${BORG_LOG} 2>&1
```

Il ne reste qu'a ajouter ce script dans Cron pour effectuer la sauvegarde et la purge tous les jours.

### Remerciements et sources

-   [https://sebsauvage.net/wiki/doku.php?id=borgbackup](https://sebsauvage.net/wiki/doku.php?id=borgbackup)
-   [https://blog.karolak.fr/2017/05/05/monter-un-serveur-de-sauvegardes-avec-borgbackup/](https://blog.karolak.fr/2017/05/05/monter-un-serveur-de-sauvegardes-avec-borgbackup/)
-   [https://blog.garamotte.net/posts/2018/01/11/fr-backup-and-restore-with-borg.html](https://blog.garamotte.net/posts/2018/01/11/fr-backup-and-restore-with-borg.html)
-   [https://connect.ed-diamond.com/Linux-Pratique/LP-098/Ne-procrastinez-plus-vos-sauvegardes-grace-a-Borg3](https://connect.ed-diamond.com/Linux-Pratique/LP-098/Ne-procrastinez-plus-vos-sauvegardes-grace-a-Borg3)