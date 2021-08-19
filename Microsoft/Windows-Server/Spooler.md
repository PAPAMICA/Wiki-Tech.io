---
title: Windows-Server - Spooler bloqué
description: Résoudre les problème de spooler d’impression
published: true
date: 2021-06-14T07:58:06.563Z
tags: 
editor: markdown
dateCreated: 2021-05-24T10:36:19.208Z
---

# Le problème du Spooler d’impression

## Contexte

Un PC sous Windows 7 Pro dans un domaine AD d’un Windows Server 2012 R2 a, quelques fois, des problèmes pour imprimer. Les copieurs sont déployés via GPO pour l’ensemble des postes, il n’y a pas de droits particuliers sur les imprimantes, pas d’impression sécurisés de mise en place, les utilisateurs sont administrateurs de leurs PC… Mais aucun accès aux impressions, elles sont toutes « grisées » dans le panneau de configuration Windows.

Un redémarrage du PC ne permet pas à tout les coups de faire fonctionner de nouveau ces imprimantes…

## Découverte & Résolution du problème

Quelques avertissements dans l’observateur d’événements Windows étaient affichés, mais pour prévenir uniquement le spooler devait être déchargé avant de pouvoir être exploité.  
En faisant quelques recherches sur le net, j’ai vu qu’une manipulation consistait à vider le dossier de tout ses fichiers : « C:\\Windows\\System32\\spool\\PRINTERS ». Il y avait quelques fichiers à l’intérieur de quelques octets, rien de bien grave à priori. J’ai suivi les indications et ai supprimé les fichiers présents – toujours le même pb, pas d’imprimantes accessibles donc pas d’impression.

Machinalement, j’ai redémarré le spooler d’impression de Windows, dans le gestionnaire de services Windows – miracle ! Les imprimantes se sont débloquées dans le panneau de configuration et l’impression a pu être de nouveau effective !

## Script de résolution du problème

Pour aller plus loin, j’ai décidé de créer un tout petit script .bat facile d’accès pour tous les utilisateurs, si le problème revenait.  
Voici le contenu du « .bat » :

```powershell
del /F /S /Q C:\Windows\System32\spool\PRINTERS\*.*
NET STOP SPOOLER && NET START SPOOLER
exit
```

**Explications** :

-   Suppression sans sommation de tous les fichiers contenus dans le dossier « C:\\Windows\\System32\\spool\\PRINTERS »
-   Arrêt du service « spooler » et redémarrage par la suite
-   exit – la commande parle d’elle-même !

Exécutez le script et vos imprimantes vont se remettre à fonctionner, qu’elles soient via le réseau ou en USB !