---
title: L'énumération basée sur l'accès
description: Sécuriser les dossiers d'un partage réseau
published: true
date: 2021-06-08T13:33:05.899Z
tags: windows, eba, énumération, droits, sécurité
editor: markdown
dateCreated: 2021-06-06T06:34:53.839Z
---



# Introduction

***L’énumération basée sur l’accès***, également ***EBA***, concerne les partages réseau.

Cette fonctionnalité de Windows Serveur permet d’afficher au sein d’un partage réseau, seulement les dossiers et fichiers dont l’utilisateur possède au minimum les droits de lectures. Les autres dossiers et fichiers présents dans ce même partage et où l’utilisateur ne possède pas les droits seront cachés.

La question première est : *pourquoi caché certains dossiers ou fichiers à des utilisateurs dans un partage réseau ?*
La réponse est relativement simple, il s’agit avant tout d’une question de sécurité.

Effectivement, un utilisateur qui ne voit pas les données qui ne le concernent pas n’aura pas la tentation d’aller voir ces données.

# Présentation de l'EBA

Nous allons prendre le cas suivant : nous avons un utilisateur du service technique qui utilise une session TSE sur laquelle il a accès à un lecteur réseau où est présent le dossier Documents de la société.

![2020-12-07_14h44_04.png](/images/2020-12-07_14h44_04.png)

Dans ce dossier partagé Documents, notre technicien peut voir l’arborescence suivante et avoir l’accès aux différents contenus des dossiers également.

![2021-06-06_08h38_56.png](/images/2021-06-06_08h38_56.png)

Nous venons de voir ce qui se passe lorsque l’EBA n’est pas actif sur le serveur où le dossier partagé est présent. aintenant, voici la vue qu’aura le même utilisateur technicien une fois l’énumération basée sur l’accès d’active.

![2020-12-07_15h12_44.png](/images/2020-12-07_15h12_44.png)

> Il est impératif pour que l’énumération basée sur l’accès fonctionne correctement, les droits NTFS doivent correctement être configurés sur le dossier partagé. Il sera nécessaire de faire la désactivation de l’héritage et de faire également la suppression du groupe DOMAINE\Utilisateurs dans les droits du dossier.
{.is-warning}

# Activation de l'EBA

Il faut se rendre sur le ***serveur où le dossier partagé est présent***. Puis depuis le gestionnaire de serveur se rendre dans : ***Services de fichiers et de stockage*** :

![2020-12-07_14h47_16.png](/images/2020-12-07_14h47_16.png)

Une fois dans ce menu, faites un ***clic droit sur le dossier partagé*** sur lequel vous souhaitez mettre l’EBA en place et sélectionnez ***Propriétés* **:

![2020-12-07_14h47_46.png](/images/2020-12-07_14h47_46.png)

Dans la nouvelle fenêtre qui s’ouvre, il faut se rendre dans ***Paramètres*** puis cocher la case ***Activer l’énumération basée sur l’accès***:

![2020-12-07_14h48_26.png](/images/2020-12-07_14h48_26.png)

Il vous suffira de valider par le bouton OK.

Félicitations ! L’énumération basée sur l’accès est maintenant active sur votre dossier partagé ! Il vous faudra maintenant faire la configuration de vos droits sur les différents dossiers.

# Droits NTFS

Je ne vais pas vous expliquer comment faire la gestion des droits NTFS sur le dossier partagé et les sous-dossiers, car il ne s’agit pas de ce sujet dans cet article.

Néanmoins, voici un aperçu des droits en place sur mon environnement où l’EBA est active :

![2020-12-07_15h11_22.png](/images/2020-12-07_15h11_22.png)


---

[*Source d'origine de la procédure*](https://www.tech2tech.fr/windows-server-lenumeration-basee-sur-lacces/) | *Rédacteur Kevin ENGEL*