---
title: L'énumération basée sur l'accès
description: 
published: false
date: 2021-06-06T06:41:43.375Z
tags: windows, eba, énumération, droits, sécurité
editor: markdown
dateCreated: 2021-06-06T06:34:53.839Z
---


# L'énumération basée sur l'accès

## Introduction

***L’énumération basée sur l’accès***, également ***EBA***, concerne les partages réseau.

Cette fonctionnalité de Windows Serveur permet d’afficher au sein d’un partage réseau, seulement les dossiers et fichiers dont l’utilisateur possède au minimum les droits de lectures. Les autres dossiers et fichiers présents dans ce même partage et où l’utilisateur ne possède pas les droits seront cachés.

La question première est : *pourquoi caché certains dossiers ou fichiers à des utilisateurs dans un partage réseau ?*
La réponse est relativement simple, il s’agit avant tout d’une question de sécurité.

Effectivement, un utilisateur qui ne voit pas les données qui ne le concernent pas n’aura pas la tentation d’aller voir ces données.

## Présentation de l'EBA

Nous allons prendre le cas suivant : nous avons un utilisateur du service technique qui utilise une session TSE sur laquelle il a accès à un lecteur réseau où est présent le dossier Documents de la société.

![2020-12-07_14h44_04.png](/images/2020-12-07_14h44_04.png)

Dans ce dossier partagé Documents, notre technicien peut voir l’arborescence suivante et avoir l’accès aux différents contenus des dossiers également.

![2021-06-06_08h38_56.png](/images/2021-06-06_08h38_56.png)

Nous venons de voir ce qui se passe lorsque l’EBA n’est pas actif sur le serveur où le dossier partagé est présent. aintenant, voici la vue qu’aura le même utilisateur technicien une fois l’énumération basée sur l’accès d’active.

![2020-12-07_15h12_44.png](/images/2020-12-07_15h12_44.png)

> Il est impératif pour que l’énumération basée sur l’accès fonctionne correctement, les droits NTFS doivent correctement être configurés sur le dossier partagé. Il sera nécessaire de faire la désactivation de l’héritage et de faire également la suppression du groupe DOMAINE\Utilisateurs dans les droits du dossier.
{.is-warning}
