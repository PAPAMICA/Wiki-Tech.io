---
title: Backup
description: Faire ses sauvegardes sous Linux
published: true
date: 2021-05-09T09:23:42.589Z
tags: 
editor: markdown
dateCreated: 2021-04-30T22:14:05.342Z
---

![Linux server backup and replication services | Dogsbody Technology](https://www.dogsbody.com/wp-content/uploads/Server-backup.png)

# Sauvegarder son poste

## Dossier à sauvegarder

## Timeshift

## rsync

# Sauvegarder le disque d'un serveur

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

Lancer la sauvegarde avec la commande suivante :put "|dcfldd if=/dev/MA\_PARTITION bs=32k sizeprobe=if statusinterval=5 | gzip" MON\_IMAGE.gz

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

`foo@bar:/# tar cvpzf backup-$(date +%d-%m-%Y).tar.gz --exclude=/backup-$(date +%d-%m-%Y).tar.gz --exclude=/proc --exclude=/tmp --exclude=/mnt --exclude=/dev --exclude=/sys --exclude=/run --exclude=/media --exclude=/var/log --exclude=/usr/src/linux-headers\* --exclude=/home /` 

Ce la va créer à la racine un fichier backup.tar.gz avec la date d'aujourd'hui tout en excluant les dossiers temporaires et inutiles pour la restauration. 

> Vous pouvez ajouter autant de répertoires à exclure de la sauvegarde que vous voulez avec le paramètre `--exclude`. En revanche, dans tout les cas il est inutile de prendre`/proc`, `/tmp`, `/dev` ,`/sys` et `/run` 

## Avec Nohup

On peut aussi lancer la commander avec `nohup` pour éviter de perdre le processus une fois le terminal coupé (éviter l'envoi signal SIGHUP alias "*signal hang up*" qui clôture l'ensemble des processus enfants lancées par le terminal virtuel) :

[`foo@bar`](mailto:foo@bar)`:/# nohup tar cvpzf backup-$(date +%d-%m-%Y).tar.gz --exclude=/backup-$(date +%d-%m-%Y).tar.gz --exclude=/proc --exclude=/tmp --exclude=/mnt --exclude=/dev --exclude=/sys --exclude=/run --exclude=/media --exclude=/var/log --exclude=/usr/src/linux-headers\* --exclude=/home --exclude=nohup.out / &`

> Remarquez la présence `&` à la fin de la commande. Cela mets le processus en arrière plan (Que vous pouvez retrouver avec la commande `jobs`). 

> Vous pouvez suivre l'avancée de la sauvegarde listant le contenu de **nohup.out** : [`foo@bar:/`](mailto:foo@bar:/)`# tail -f nohup.out`

> Faites attention à bien exclure le fichier **nohup.out** de la sauvegarde. Celui-ci est crée dans le répertoire où vous travaillez (En utilisant la variable **$PWD**).

# Sauvegarder avec Swiss Backup

# Sauvegarder un serveur avec Active Backup de Synology

## Présentation

## Configuration de la sauvegarde