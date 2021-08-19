---
title: Linux - Installer un Raspberry Pi
description: Toute l'installation sans écran ni clavier !
published: true
date: 2021-07-16T08:43:34.842Z
tags: linux, raspberry-pi
editor: markdown
dateCreated: 2021-07-16T07:58:24.695Z
---

![](https://www.kubii.fr/img/cms/Autres/RPi-Logo-Landscape-Reg-SCREEN.png)

# Préparation
Avant même de commencer l'installation, il faut savoir ce dont vous avez besoin ! 

## Matériels essentiels
- Un [Raspberry Pi](https://amzn.to/3rfci9X)
- Une [carte micro SD de 16 Go minimum](https://amzn.to/3B98knL)
- Une [alimentation](https://amzn.to/3hIEE9t)
- Un [câble ethernet](https://amzn.to/3klQ0C4) (si pas de Wi-Fi)
- Un [lecteur de carte micro SD](https://amzn.to/3xK2WW1)

## Matériels optionnels
- Un [boitier pour le Raspberry Pi](https://amzn.to/3B8mo0U)
- Un [système de refroidissement](https://amzn.to/3B8mo0U) (dissipateur ou venilateur)
- Un [adaptateur POE](https://amzn.to/3yYVV3S)
- Un [rack pour baie](https://amzn.to/2Ug0wQF) ou un [rack pour boitier electrique](https://amzn.to/3hGo0r6)

## Téléchargements
Il vous faudra aussi un ordinateur sous Linux ou Windows avec un editeur de texte de votre choix et le logiciel [BalenaEtcher](https://www.balena.io/etcher/).

Avant de commencer, téléchargez la dernière version de l'image Raspberry Pi OS (anciennement Raspbian) directement via [ce lien](https://raspberry-pi.fr/download/raspbian_latest.zip).

# Installation

On va donc commencer par installer le système d'exploitation sur la carte micro SD :

1 - Lancez le logiciel Balena Etcher
![install_1.png](/images/linux/raspberry-pi/install_1.png)
2 - Selectionnez le fichier "raspbian_latest.zip" précédemment téléchargé
![install_2.png](/images/linux/raspberry-pi/install_2.png)
3 - Selectionnez votre carte micro SD
![install_3.png](/images/linux/raspberry-pi/install_3.png)
4 - Cliquez sur "Flash!" etpatientez quelques minutes
![install_4.png](/images/linux/raspberry-pi/install_4.png)

Et voilà, votre système est installé, on va pouvoir passer à la configuration !

# Configuration
## Activation du SSH
Le SSH est désactivé par défaut sur les Raspberry Pi (depuis l'attaque massive visant les objects connectés en 2016), il faut donc l'activer manuellement avant de démarrer le RPi :
L'activation de fait très simplement avec la création d'un fichier `ssh` (sans extension) à la racine de la partition `boot` sur la carte micro SD.

## Connexion au Wi-Fi
Si vous ne disposez pas de câbles ethernet, vous pouvez configurer le RPi pour qu'il se connecte à votre Wi-Fi au démarrage.

Afin de configurer la connexion Wi-fi lors du démarrage de la Pi, nous allons créer un fichier `wpa_supplicant.conf`, situé à la racine de la partition `boot` de la carte.

Pour cela, nous allons utiliser l’application bloc-note (ou tout autre éditeur de texte, tel que SublimText, NotePad, etc.). Le fichier devra contenir les lignes suivantes :
```
country=fr
update_config=1
ctrl_interface=/var/run/wpa_supplicant

network={
 scan_ssid=1
 ssid="MaBoxInternet"
 psk="ClefSecurite"
}
```

A la ligne `ssid`, vous allez remplacer `MaBoxInternet` par le nom de votre box, `Livebox-5678` par exemple. Pour le champ `psk`, cela correspond au code de sécurité de votre box, remplacez donc `ClefSecurite` par la clef de votre box.

# Démarrage
Maintenant que le système Raspberry Pi OS est configuré sur votre carte micro SD, vous allez pouvoir booter votre RPi sur la carte en l'inserant dans l'emplacement prévu et en alimentant la machine !

Après quelques minutes, avec un petit scan réseau, vous devriez trouver votre nouveau petit joujou et vous devriez pouvoir vous y connecter en SSH avec l'identifiant et mot de passe : `pi` / `raspberry`.