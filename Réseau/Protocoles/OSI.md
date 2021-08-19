---
title: Modèle OSI
description: Comprendre le modèle OSI
published: true
date: 2021-06-14T08:04:53.002Z
tags: 
editor: markdown
dateCreated: 2021-05-24T10:36:43.215Z
---

![smart building Protocol | smart home Karen Make Smart Engineers Co.](https://bmskaren.ir/wp-content/uploads/2020/11/bmskaren-85-1.png)

# Présentation

C’est un modèle de communications entre ordinateurs proposé par l’ISO (Organisation Internationale de Normalisation) qui décrit les fonctionnalités nécessaires à la communication et l’organisation de ces fonctions.

Le principe du modèle OSI est de cadrer le protocole TCP/IP. Il est basé sur 7 couches de communication :

![Image associée](https://upload.wikimedia.org/wikipedia/commons/8/8d/OSI_Model_v1.svg)

> *Les couches ne peuvent communiquer qu’entre elles (réseau / réseau, liaison / liaison mais pas liaison / transport).*
{.is-info}

## Implémentation des 7 couches sur les équipements

Chaque équipement réseau (PC, switch, routeur,…) a ses propres caractéristiques et donc a besoin de toutes les couches du modèles OSI ou seulement d’une partie.

![](https://i0.wp.com/reussirsonccna.fr/wp-content/uploads/2012/02/7couches_archi11.png?resize=604%2C363)

Dans ce schéma, on remarque que PC1 et PC2 ont besoin de **toutes les couches** du modèle OSI. En effet, il faut qu’on puisse envoyer des paquets avec notre carte réseau (couches basses)et qu’on utilise une application (firefox, outlook,…) pour communiquer avec nos voisins (couches hautes).

En revanche, le switch n’a besoin **que des couches 1 et 2** du modèles OSI, car cet équipement a besoin de connaitre uniquement **votre adresse MAC** (qui se trouve au niveau 2) pour pouvoir commuter votre trame vers le bon port.

Le routeur a lui besoin **des couches 1, 2 et 3**. La couche 3 (réseau) lui permet de faire communiquer des réseaux entre eux. Par exemple, quand je surf sur Internet, c’est le routeur qui fait le lien entre mon réseau LAN et le réseau Internet, et ce lien est établit au niveau 3.

# Les couches

## 1 – Physique

Cette couche se charge de la transmission et la réception des données informatique au format binaire (0 et 1).

-   sur une paire de cuivre, c’est un signal électrique qui définit le 0 et un autre signal électrique qui définit le 1.
-   sur une fibre optique, c’est la lumière envoyée dans la silice qui s’en occupe
-   sur du sans-fil, c’est la modulation
-   …

## 2 – Liaison de données

Cette couche définit comment la transmission des données est effectuée entre 2 machines adjacentes. Par exemple, un PC connecté à un switch, une imprimante connecté à un switch, deux routeurs connectés entre eux…

La notion d’adressage physique est présente. Quand votre carte réseau recoit des 0 et des 1, il faut qu’elle vérifie si c’est bien pour vous. Par exemple pour la technologie Ethernet, on parle d’adresse MAC – Medium Access Control. Votre carte réseau a une adresse MAC qui l’identifie dans le réseau.

Cette couche gère aussi la détection d’erreur de transmission. Par exemple, quand l’émetteur envoi la séquence 11001 et le destinataire recoit 11011, la couche va le détecter.

Pour ethernet, la séquence de données envoyée par l’émetteur est appelé une trame (ou frame en anglais)

## 3 – Réseau

On a vu que la couche 2 gère la communication entre machines adjacente uniquement, il faut bien qu’une couche se charge de la communication entre machines qui sont physiquement pas connectés entre elles; c’est la couche 3 qui s’en charge. Avec cet adressage logique, on peut délivrer les données à l’autre bout de la planète.

Pour faire une analogie, pensez que l’adressage logique correspond à l’adresse postale que vous mettez sur une enveloppe.

La couche 3 va aussi se charger de trouver le meilleur chemin pour acheminer les données jusqu’à la destination.

Aujoud’hui, l’adressage logique utilisée est le protocole IP et la sélection du meilleur chemin s’effectue par les routeurs avec l’aide des protocoles de routage (RIP, OSPF, BGP…)

## 4 – Transport

Cette couche se charge de plusieurs fonctionnalités essentielles:

-   gère les problème de transport entre les machines (c’est toujours plus sympa si je reçoi un mail en entier)
-   et donc fiabilise les communications (si on perd des données, on demande une retransmission des données manquantes)
-   gère le contrôle de flux (vous téléchargez plus vite quand vous êtes sur du 100Mb/s que sur 2Mb/s… merci à cette couche)
-   fait le lien entre les communications réseaux et les applicatifs (je peux avoir mon browser ouvert en même temps que ma messagerie et mon téléchargement de fichier)

## 5 – Session

Cette couche gère l’ouverture et la fermeture des sessions entre inter-applicatif et intra-applicatif (avec mon browser, je peux ouvrir plusieurs onglets).

## 6 – Présentation

Cette couche formate les données pour qu’elles deviennent compréhensibles par l’application qui les a demandées.

## 7 – Application

Cette couche fait l’interface entre l’homme et la machine. Votre browser (firefox, safari,chrome…), votre logiciel de messsagerie (outlook, thunderbird…) sont des applications.

# Résumé en vidéo

Voici une petite vidéo qui résume tout ça : [Youtube](https://youtu.be/w1QcbAJ9RcY)