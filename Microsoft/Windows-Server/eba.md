---
title: L'énumération basée sur l'accès
description: 
published: false
date: 2021-06-06T06:35:32.268Z
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

![2020-12-07_14h43_13.png](/images/2020-12-07_14h43_13.png) ![2020-12-07_14h44_04.png](/images/2020-12-07_14h44_04.png)