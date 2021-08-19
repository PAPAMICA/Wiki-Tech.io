---
title: Xpenology sur ESXi
description: Virtualiser DSM simplement !
published: true
date: 2021-06-14T08:00:19.298Z
tags: 
editor: markdown
dateCreated: 2021-05-24T10:36:23.512Z
---

![](https://i2.wp.com/labo-tech.fr/wp-content/uploads/2019/11/020_logo_esxi.jpg?resize=920%2C345&ssl=1)

# Présentation

Il est possible d’utiliser le système DSM (OS des nas Synology) sur un VM ESXi grâce au “hack” Xpenology.

# Prérequis

-   Un serveur tournant sous ESXi 6.5 ou plus.
-   L’image du disque : [synoboot vmdk](https://mega.nz/#!fdBWBJYB!P3MbGY2v_X_udUhaSgVBQZ74KNRf7vtjMCO39u1I91Y)
-   Le logiciel suivant (qui inclut tout ce dont on a besoin) : [Xpenology\_Tools](https://partages.papamica.fr/files/LOGICIELS/TECH/Xpenology/)
-   De la patience et de la persévérance (et oui !)

# Installation

## Préparation de la clé USB

-   Lancez Xpenology Tools, rendez-vous dans l’onglet Download et téléchargez une version du **loader** que vous souhaitez. (pour ma part : la version 1.03b pour le DS3615XS)

![](https://i1.wp.com/labo-tech.fr/wp-content/uploads/2019/11/syno2.png?resize=920%2C787&ssl=1)

-   Créer un dossier et décompresser le dossiers d’image Synoboot ainsi que le loader.
-   Récupérez un **SN** pour le DS3615XS avec l’outils **Serial Generator** dans l’onglet Portable Tools.
-   Lancez le logiciel **OSFMOUNT** dans l’onglet Portable Tools et configurez le de la façon suivante :

![](https://i2.wp.com/labo-tech.fr/wp-content/uploads/2019/11/syno3.png?resize=920%2C733&ssl=1)

-   Une nouvelle partition est disponible dans “**Ce PC**“, ouvrez la et modifier le fichier **grub.cfg** dans le dossier grub avec **Notepad++**.
-   Remplacez les lignes suivantes :
    -   set sn=**1130LWN02016**1     – Le SN que vous avez récupérer à l’étape 3
    -   set mac1=**bc5ff4cb64e4**      – L’adresse MAC de la carte réseau que vous allez mettre dans ESXi.
-   **Commentez** les lignes pour le boot de la façon suivante :

![](https://i1.wp.com/labo-tech.fr/wp-content/uploads/2019/11/vmsyno.png?resize=920%2C753&ssl=1)

-   **Sauvegardez le fichier** et cliquez sur Dismount dans OSFMOUNT.

## Installation de la machine virtuelle

-   Connectez vous à votre ESXi.
-   Télécharger les fichiers synoboot.vmdk et synoboot.img dans un **dossiers** sur l’ESXi.
-   Créez une nouvelle VM :

![](https://i2.wp.com/labo-tech.fr/wp-content/uploads/2019/11/vmsyno2.png?resize=920%2C582&ssl=1)

![](https://i1.wp.com/labo-tech.fr/wp-content/uploads/2019/11/vmsyno3.png?resize=920%2C584&ssl=1)

![](https://i1.wp.com/labo-tech.fr/wp-content/uploads/2019/11/vmsyno4.png?resize=920%2C583&ssl=1)

-   Supprimez les disques, supprimez les controleurs, supprimez le lecteur CD, changez le USB3.0, changez le nombre de CPU, changer le nombre de RAM, changer la carte réseau comme le Screenshot en dessous en remplaçant l’adresse mac par celle que vous avez rentrez à l’étape 6 :

![](https://i0.wp.com/labo-tech.fr/wp-content/uploads/2019/11/vmsyno5.png?resize=920%2C583&ssl=1)

-   Ajoutez un **controleur SATA** et un nouveau **Disque dur existant** en allant chercher votre image **synoboot.vmdk** :

![](https://i0.wp.com/labo-tech.fr/wp-content/uploads/2019/11/vmsyno6.png?resize=920%2C584&ssl=1)

-   Ajoutez de nouveau un **controleur SATA** et un **Nouveau disque standard**, c’est ce disque sur lequel sera stocké vos données, à vous de choisir la taille souhaitée :

![](https://i1.wp.com/labo-tech.fr/wp-content/uploads/2019/11/vmsyno7.png?resize=920%2C584&ssl=1)

-   La configuration de la VM est terminée, démarrez la.
-   Sur votre ordinateur connecté au même réseau que votre VM, lancez Synology Assistant, vous devriez voir votre nouveau DiskStation :

![](https://i1.wp.com/labo-tech.fr/wp-content/uploads/2019/11/vmsyno8.png?resize=920%2C603&ssl=1)

-   Faites un clique droit et “Installer”
-   Aller chercher le fichier **PAT** téléchargé lors de la préparation :

![](https://i0.wp.com/labo-tech.fr/wp-content/uploads/2019/11/vmsyno9.png?resize=920%2C637&ssl=1)

-   Choisissez un mot de passe ainsi qu’un nom pour le NAS.
-   Configurez l’adressage réseau.
-   Laissez DSM s’installer :

![](https://i0.wp.com/labo-tech.fr/wp-content/uploads/2019/11/vmsyno91.png?resize=920%2C726&ssl=1)

-   Une fois l’installation terminée, cliquez ici :

![](https://i0.wp.com/labo-tech.fr/wp-content/uploads/2019/11/vmsyno92.png?resize=920%2C725&ssl=1)

-   Connectez vous, faites les derniers réglages (ignorez l’étape du Quickconnect) et vous voilà sur votre nouveau NAS !

![](https://i1.wp.com/labo-tech.fr/wp-content/uploads/2019/11/vmsyno93-1.png?resize=920%2C417&ssl=1)

Et vous voilà avec un NAS tournant sous DSM 6.2 avec presque toutes les fonctionnalités de Synology !

Forum Xpenology : [https://xpenology.com/forum/](https://xpenology.com/forum/) 

> Attention : N’oubliez pas que vous utilisez un “hack” de DSM de Synology, le système peut être instable et surtout ce dernier n’est pas mise à jour automatiquement. (avant de vous lancer dans une mise à jour, renseignez vous sur le forum de Xpenology)
{.is-warning}