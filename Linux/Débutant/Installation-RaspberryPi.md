---
title: Linux - Installer un Raspberry Pi
description: Toute l'insallation sans écran ni clavier !
published: true
date: 2021-07-16T07:58:24.695Z
tags: linux, raspberry-pi
editor: markdown
dateCreated: 2021-07-16T07:58:24.695Z
---

# Préparation
Avant même de commencer l'installation, il faut savoir ce dont vous avez besoin ! 

## Matériels essentiels
- Un Raspberry Pi
- Une carte micro SD de 16 Go minimum
- Une alimentation
- Un câble ethernet (si pas de Wi-Fi)
- Un lecteur de carte micro SD

## Matériels optionnels
- Un boitier pour le Raspberry Pi
- Un système de refroidissement (dissipateur ou venilateur)
- Un adaptateur POE
- Un rack pour baie ou un rack pour boitier electrique

## Téléchargements
Il vous faudra aussi un ordinateur sous Linux ou Windows avec un editeur de texte de votre choix et le logiciel [BalenaEtcher](https://www.balena.io/etcher/).

Avant de commencer, téléchargez la dernière version de l'image Raspberry Pi OS (anciennement Raspbian) directement via [ce lien](https://raspberry-pi.fr/download/raspbian_latest.zip).

# Installation
On va donc commencer par installer le système d'exploitation sur la carte micro SD :

1 - Lancez le logiciel Balena Etcher
2 - Selectionnez le fichier "raspbian_latest.zip" précédemment téléchargé
3 - Selectionnez votre carte micro SD
4 - Cliquez sur "Flash!" etpatientez quelques minutes

Et voilà, votre système est installé, on va pouvoir passer à la configuration !