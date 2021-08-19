---
title: Jeedom - Installation sur Raspberry Pi
description: Préparer son serveur domotique sur un RPi !
published: true
date: 2021-07-16T15:50:31.316Z
tags: raspberry-pi, jeedom
editor: markdown
dateCreated: 2021-07-16T15:50:29.479Z
---

![](https://monclavier.fr/fichiers/2015/11/logo-jeedom-650x150.png)
# Prérequis
- Un Raspberry Pi 3B+ de préférence (ou au dessus) fonctionnel (voir : [Installation-RaspberryPi](/Linux/Débutant/Installation-RaspberryPi))
- Mettre une adresse IP fixe
- Une connexion internet

# Installation
1 - Connectez vous en SSH à votre RPi
2 - Mettez le à jour avec :
```bash
sudo apt update && sudo apt upgrade
```
3 - Lancez l'installation de Jeedom avec la commande suivante :
```bash
wget -O- https://raw.githubusercontent.com/jeedom/core/master/install/install.sh | sudo bash
```
> L'installation peut prendre plusieurs minutes, attention à ne pas l'interrompre.
{.is-warning}

4 - Accèdez à l'interface web en entrant l'adresse ip de votre raspberry pi dans un navigateur internet :
![install_1.png](/images/domotique/jeedom/installation/install_1.png)
5 - Connectez vous avec l'identifiant `admin` et le mot de passe `admin` et changez le mot de passe comme demandé
6 - Connectez ou créez un compte Jeedom Market (pour les plugins) :
![install_2.png](/images/domotique/jeedom/installation/install_2.png)

> Et voilà ! Votre serveur de domotique Jeedom est installé !
{.is-success}
