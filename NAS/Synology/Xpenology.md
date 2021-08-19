---
title: Xpenology sur du hardware dédié
description: Profiter de DSM sur son propre serveur !
published: true
date: 2021-06-14T07:59:49.297Z
tags: 
editor: markdown
dateCreated: 2021-05-24T10:36:25.643Z
---

![](https://i0.wp.com/labo-tech.fr/wp-content/uploads/2019/11/syno6.jpg?resize=920%2C518&ssl=1)

# Présentation

Il est possible d’utiliser le système DSM (OS des nas Synology) sur du matériel dédié grâce au “hack” Xpenology.

# Prérequis

-   Un ordinateur ou un serveur (pour ma part : un Microserveur HP Proliant Gen8)
-   Une clé USB de minimum 2 Go
-   Le logiciel suivant (qui inclut tout ce dont on a besoin) : [Xpenology\_Tool](https://mega.nz/#F!BtViHIJA!uNXJtEtXIWR0LNYUEpBuiA)
-   De la patience et de la persévérance (et oui !)

# Installation

## Préparation de la clé USB

-   Branchez votre clé USB à votre ordinateur.
-   Démarrez Xpenology\_Tool et lancez le logiciel **USB Device View** dans l’onglet Portable Tools.
-   Identifiez votre clé USB (Mass storage) et mettez de côté les informations suivantes : **PID** & **VID**.

![](https://i1.wp.com/labo-tech.fr/wp-content/uploads/2019/11/syno1.png?resize=920%2C695&ssl=1)

-   Rendez-vous dans l’onglet Download et téléchargez une version du **loader** que vous souhaitez. (pour ma part : la version 1.03b pour le DS3617XS)

![](https://i2.wp.com/labo-tech.fr/wp-content/uploads/2019/11/syno2.png?resize=920%2C787&ssl=1)

-   Créez un dossier sur votre bureau, et déposez le fichier **synoboot.img** dedans.
-   Récupérez un **SN** pour le DS3617XS avec l’outils **Serial Generator** dans l’onglet Portable Tools.
-   Lancez le logiciel **OSFMOUNT** dans l’onglet Portable Tools et configurez le de la façon suivante :

![](https://i1.wp.com/labo-tech.fr/wp-content/uploads/2019/11/syno3.png?resize=920%2C733&ssl=1)

-   Une nouvelle partition est disponible dans “**Ce PC**“, ouvrez la et modifier le fichier **grub.cfg** dans le dossier grub avec **Notepad++**.
-   Remplacer les lignes suivantes :
    -   set vid=0x8564                      – Le VID de votre clé USB
    -   set pid=0x1000                     – Le PID de votre clé USB
    -   set sn=1130LWN020161     – Le SN que vous avez récupérer à l’étape 6
    -   set mac1=bc5ff4cb64e4      – L’adresse MAC de la carte réseau de votre serveur (dans le bios).
-   Sauvegardez le fichier et cliquez sur Dismount dans OSFMOUNT.
-   Ouvrez Rufus pour graver l’image sur notre clé USB :

![](https://i1.wp.com/labo-tech.fr/wp-content/uploads/2019/11/syno4.png?resize=870%2C1024&ssl=1)

## Installation du serveur

-   Une fois la clé préparer, il faut configurer le BIOS de votre serveur pour booter sur la clé USB en priorité.
-   Démarrer votre serveur avec la clé.
-   Sur votre PC, télécharger le **fichier .PAT** pour votre version du loader (Dans cet exemple, la version DSM 6.2 pour le DS3617XS)

![](https://i0.wp.com/labo-tech.fr/wp-content/uploads/2019/11/syno5.png?resize=920%2C787&ssl=1)

-   Sur votre ordinateur connecté au même réseau, lancez Synology Assistant, vous devriez voir votre nouveau DiskStation :

![](https://i1.wp.com/labo-tech.fr/wp-content/uploads/2019/11/vmsyno8.png?resize=920%2C603&ssl=1)

-   Faites un clique droit et “Installer”
-   Aller chercher le fichier PAT téléchargé lors de la préparation :

![](https://i0.wp.com/labo-tech.fr/wp-content/uploads/2019/11/vmsyno9.png?resize=920%2C637&ssl=1)

-   Choisissez un mot de passe ainsi qu’un nom pour le NAS.
-   Configurez l’adressage réseau.
-   Laissez DSM s’installer :

![](https://i0.wp.com/labo-tech.fr/wp-content/uploads/2019/11/vmsyno91.png?resize=920%2C726&ssl=1)

-   Une fois l’installation terminée, cliquez ici :

![](https://i0.wp.com/labo-tech.fr/wp-content/uploads/2019/11/vmsyno92.png?resize=920%2C725&ssl=1)

-   Connectez vous, faites les derniers réglages (ignorez l’étape du Quickconnect) et vous voilà sur votre nouveau NAS !

Et vous voilà avec un NAS tournant sous DSM 6.2 avec presque toutes les fonctionnalités de Synology !

Forum Xpenology : [https://xpenology.com/forum/](https://xpenology.com/forum/) 

> **Attention :** N’oubliez pas que vous utilisez un “hack” de DSM de Synology, le système peut être instable et surtout ce dernier n’est pas mise à jour automatiquement. (avant de vous lancer dans une mise à jour, renseignez vous sur le forum de Xpenology)
{.is-warning}