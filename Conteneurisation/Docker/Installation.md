---
title: Installation
description: Installation de Docker sous Debian, Ubuntu, Arch, Manjaro, CentOS, Fedora, Redhat, MacOS et même Windows !
published: true
date: 2021-06-14T07:18:48.666Z
tags: 
editor: markdown
dateCreated: 2021-05-24T10:34:24.029Z
---

# Installation
## Debian & Ubuntu

1 - Mettez à jour votre liste de packages existante :

```bash
sudo apt update
```

2 - Installez quelques packages prérequis qui permettent à `apt` d’utiliser des packages via HTTPS :

```bash
sudo apt install apt-transport-https ca-certificates curl gnupg2 software-properties-common
```

3 - Ajoutez la clé GPG du référentiel Docker officiel à votre système :

```bash
curl -fsSL https://download.docker.com/linux/debian/gpg | sudo apt-key add -
```

4 - Ajoutez le référentiel Docker aux sources APT :

```bash
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/debian $(lsb_release -cs) stable"
```

5 - Mettez à jour la base de données des packages avec les packages Docker du repo nouvellement ajouté :

```bash
sudo apt update
```

6 - Installez Docker :

```bash
sudo apt install docker-ce
```

7 - Activez et lancer le daemon Docker :

```bash
sudo systemctl enable docker
sudo systemctl start docker
```

## Arch & Manjaro

1 - Mettez à jour votre liste de packages existante et votre système :

```bash
sudo pacman -Syu
```

 2 - Installez Docker :

```bash
sudo pacman -S docker
```

 3 - Activez et lancer le daemon Docker :

```bash
sudo systemctl start docker
sudo systemctl enable docker
```

## CentOS & Fedora

1 - Utilisez DNF pour ajouter et activer le référentiel officiel Docker CE. :

```bash
sudo dnf config-manager --add-repo=https://download.docker.com/linux/centos/docker-ce.repo
```

Ou pour Fedora :

```bash
sudo dnf config-manager --add-repo=https://download.docker.com/linux/fedora/docker-ce.repo
```

 2 - Installez Docker :

```bash
sudo dnf install docker-ce
```

 3 - Activez et démarrer le service Docker :

```bash
sudo systemctl enable --now docker
```

## MacOS

Docker Desktop pour Mac est une application de bureau facile à installer pour créer, déboguer et tester des applications conteneurisées sur un Mac. Docker Desktop pour Mac est un environnement de développement complet profondément intégré à la structure, au réseau et au système de fichiers de l'hyperviseur Mac OS. Docker Desktop - Mac est le moyen le plus rapide et le plus fiable d'exécuter Docker sur un Mac.

Pour une liste des prérequis, veuillez consulter [la documentation](https://docs.docker.com/docker-for-mac/install/#system-requirements).

### **Télécharger Docker :**

-   [**📦 Mac avec processeur Intel**](https://desktop.docker.com/mac/stable/amd64/Docker.dmg)
-   [**📦 Mac avec processeur Apple**](https://desktop.docker.com/mac/stable/arm64/Docker.dmg)
{.links-list}

### Installation

Double-cliquez sur `Docker.dmg` pour démarrer l'installation.

Lorsque l'installation est terminée et que Docker démarre, la baleine dans la barre d'état supérieure indique que Docker est en cours d'exécution et accessible à partir d'un terminal.

![Whale in menu bar](https://d1q6f0aelx0por.cloudfront.net/icons/whale-in-menu-bar.png)

## Windows

Je vais me permettre de te tutoyer juste quelques lignes.  
Si tu es sous Windows, installe Linux et on verra plus tard pour Docker 😘  
Plus sérieusement, le client Docker pour Windows n’est pas foufou : il utilise WSL pour faire une VM et ensuite installer Docker dedans.  
Passe directement par un Debian sous WSL et fait l’installation de Docker dedans.  
Bon courage ! Paix à ton âme. Il est jamais trop tard pour entrée dans la lumière. 🖖

## Vérification de l'installation

Il suffit pour cela de vérifier avec la commande suivante : 

```bash
sudo docker version
```

C'est censé vous renvoyer : `docker <version>`

# Configuration

## Utiliser les commandes Docker sans Sudo

Par défaut, seuls les utilisateurs disposant de privilèges root ou sudo peuvent exécuter ou gérer Docker. Si vous souhaitez exécuter docker sans privilèges root ou sans avoir à ajouter sudo à chaque fois, il suffit d'ajouter son utilisateur au groupe docker :

```bash
sudo usermod -aG docker $USER
```

## Déplacer le dossier Docker

Le répertoire de données standard utilisé pour docker est `/var/lib/docker`, et comme ce répertoire stockera toutes vos images, volumes, etc., il peut devenir assez volumineux assez rapidement

Si vous souhaitez déplacer le répertoire de données du docker vers un autre emplacement, vous pouvez suivre les étapes simples suivantes.

###  **Arrêtez le daemon Docker :**

```bash
sudo service docker stop
```

### **Ajoutez un fichier de configuration pour indiquer au daemon docker quel est l'emplacement du nouveau répertoire de données :**

En utilisant votre éditeur de texte préféré, ajoutez un fichier nommé `daemon.json` sous le répertoire `/etc/docker`. Le fichier doit avoir ce contenu:

```json
{ 
   "data-root": "/path/to/your/docker" 
}
```

*Evidemment pensez à changer “*`*/path/to/your/docker*`*”  avec le chemin de votre dossier voulu.*

### Copiez le répertoire de données actuel dans le nouveau

```bash
sudo rsync -aP /var/lib/docker/ /path/to/your/docker
```

### Renommez l'ancien répertoire de Docker

```bash
sudo mv /var/lib/docker /var/lib/docker.old
```

*Ceci est juste une vérification de cohérence pour voir que tout va bien et que le daemon docker utilisera correctement le nouvel emplacement pour ses données.*

### Redémarrez le daemon Docker

```bash
sudo service docker start
```

### Testez !

Si tout va bien, vous ne devriez voir aucune différence dans l'utilisation de vos conteneurs Docker. Lorsque vous êtes sûr que le nouveau répertoire est utilisé correctement par le daemon docker, vous pouvez supprimer l'ancien répertoire de données.

```bash
sudo rm -rf /var/lib/docker.old
```

# Utilisation de base de Docker

Si tout se passe bien, à ce stade vous devriez être en mesure de créer vos propres conteneurs sur votre machine.

Nous allons voir dans cette section une des nombreuses possibilités d’utilisation de Docker. Par exemple je souhaite avoir un environnement ubuntu vierge. Via la commande ci-dessous je vais tout simplement récupérer la dernière image d’un conteneur debian et lancer un conteneur local associé.

```bash
docker run ubuntu
```

Vous pouvez bien évidemment changer “ubuntu” par un autre système linux , ou par un conteneur plus précis (comme une image debian avec nginx installé). Je vous invite à vous rendre sur le [Hub Docker](https://hub.docker.com/explore) pour voir toutes les images disponibles.

Vous pouvez également, simplement récupérer l’image via un :

```bash
docker pull ubuntu
```

Une fois que votre conteneur est créé et lancé, il vous faut accéder à celui-ci. Avec cette commande vous allez accéder au bash de votre conteneur et ainsi modifier ou ajouter des fonctionnalités manuellement.

```bash
docker run -it ubuntu bash
```

Voilà un rapide et simple exemple pour vous montrer le fonctionnement le plus basique de Docker. Bien évidemment cette solution va bien au-delà de ça. Vous pouvez aussi créer vos propres images personnalisées via des scripts pour des besoins précis.

Si vous voulez en savoir plus, rendez-vous sur le site de Docker.