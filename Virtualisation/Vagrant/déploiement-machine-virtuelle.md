---
title: Déploiement d'une machine virtuelle et explication du Vagrantfile
description: Ici, nous réaliserons le déploiement d'une machine virtuelle décrite dans un Vagrantfile avec quelques explications sur le contenu du fichier et les options ajoutés.
published: true
date: 2022-05-09T14:05:14.251Z
tags: linux, windows, macos, vagrant, scripting
editor: markdown
dateCreated: 2022-05-09T10:25:53.947Z
---

![external-content.duckduckgo.com.png](/external-content.duckduckgo.com.png)

# Introduction

Imaginons que vous souhaitez tester une application sur une machine virtuelle.
Pour ce faire, vous devez ouvrir votre hyperviseur (VMware Workstation, VirtualBox...), cliquer sur une interface graphique afin de choisir votre ISO, vos ressources compute (CPU/RAM/DISK), préparer le réseau, lancer l'installation, clic-clic-clic sur les menus...

C'est très rébarbatif et absolument pas gratifiant techniquement parlant lorsque nous connaissons que trop bien le processus.

Tandis qu'avec Vagrant, voici les étapes à réaliser afin de déployer une machine virtuelle sur mon poste de travail.
Tout passe par la rédaction d'un Vagrant-File qui va permettre de déclarer l'infrastructure que vous souhaitez déployer.

Retrouvez le fichier utilisé directement sur mon Github:
- [📂 Vagrantfile (github.com/Lucroz94)](https://github.com/Lucroz94/formations-eazytraining-cursus-devops/blob/main/Vagrant/lab-3/Vagrantfile)
{.links-list}

# Déploiement d'une machine virtuelle
Ici, nous allons déployer une machine virtuelle basée sur l'image ubuntu/trusty64 tout en variabilisant le nombre de coeur vCPUs ainsi que la mémoire RAM de la machine et son hostname dans VirtualBox.

## Installation de Vagrant 
Je vous invite à lire la [documentation officielle](https://www.vagrantup.com/docs/installation) pour installer la dernière version sur votre machine

## Création du fichier Vagrant
### Créer un répertoire de travail.
```bash
mkdir /chemin/dossier
```

### Initialiser ce répertoire auprès de Vagrant 
```bash
vagrant init ubuntu/trusty64
```
Cette commande permet à Vagrant de vous créer un Vagrantfile rempli de commentaires afin de vous guider dans la rédaction de celui-ci.
> L'argument `-m` permet d'avoir un Vagrantfile minimal.
{.is-info}

### Modifier le Vagrantfile 
Voici un exemple de fichier pour une machine virtuelle avec 2go de RAM et 2 vCPU :
```ruby
# -*- mode: ruby -*-
# vi: set ft=ruby :

RAM = 2048
CPU = 2

Vagrant.configure("2") do |config|

  config.vm.box = "ubuntu/trusty64"
  config.vm.provider "virtualbox" do |v|
    v.name = "customVM"
    v.cpus = CPU
    v.memory = RAM
    end
  end
 ```
> Attention ! L'indentation est extrêmement importante !
{.is-warning}
### Valider la configuration
Une fois le fichier enregistré, toujours dans le répertoire de travail, utiliser la commande suivante afin de vérifier la syntaxe du fichier : 
```bash
vagrant validate
```


## Déployer votre machine virtuelle
Utilisez la commande suivante pour lancer le déploiement :
```bash
vagrant up
```

### Connexion à la machine virtuelle
Toujours en utilisant la commande `vagrant`:
```bash
vagrant ssh
```
> Les credentials par défaut sont vagrant/vagrant
{.is-info}

## Destruction de la machine virtuelle
Une fois le test de connexion fait et le fonctionnement validé, vous pouvez forcer la destruction de votre environnement :
```bash
vagrant destroy -f
```


# Description du Vagrant-File

En début de fichier, nous avons l'équivalent du shebang en bash, c'est en Ruby et cela permet de déclarer la suite. C'est obligatoire.

Ensuite, nous annonçons nos variables. Elles peuvent être mises au début comme à la fin, ça n'est pas important, car le Vagrantfile peut récupérer l'information de la variable n'importe où. Je préfère pour un souci pédagogique et de clarté, ainsi que de rapidité si je dois les modifier au besoin, les déclarer au tout début du fichier.

Puis nous avons la partie configuration de l'objet `config`.

Cet objet `config` contient l'argument `vm.box` afin de déclarer [l'image à utiliser](https://app.vagrantup.com/ubuntu/boxes/trusty64).

L'argument `vm.provider` permet de déclarer le provider de déploiement (ici VirtualBox) avec pour objet `v` (les prochains arguments pour la configuration dans VirtualBox sont raccourcis en `v` mais cela pourrait être n'importe quoi).

`v.name` = Hostname dans VirtualBox (mais pas celui de la machine à l'intérieur ! Pour cela, un plugin est nécessaire).
`v.cpus` = La variable de CPU déclarée plus haut, ici, 2 vCPUs.
`v.memory` = la variable de mémoire RAM déclarée plus haut, ici 2GB de RAM.

Encore une fois, vérifiez bien l'indentation afin de correctement clôturer chaque bloc de configuration par un `end` sinon vous aurez une erreur de syntaxe remontée par la commande `vagrant validate`.