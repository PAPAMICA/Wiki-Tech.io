---
title: Déploiement d'une machine virtuelle et explication du Vagrantfile
description: Ici, nous réaliserons le déploiement d'une machine virtuelle décrite dans un Vagrantfile avec quelques explications sur le contenu du fichier et les options ajoutés.
published: true
date: 2022-05-09T10:25:53.947Z
tags: linux, windows, macos, vagrant, scripting
editor: markdown
dateCreated: 2022-05-09T10:25:53.947Z
---

# Déploiement d'une machine virtuelle

Imaginons que vous souhaitez tester une application sur une machine virtuelle.
Pour ce faire, vous devez ouvrir votre hyperviseur (VMware Workstation, VirtualBox...), cliquer sur une interface graphique afin de choisir votre ISO, vos ressources compute (CPU/RAM/DISK), préparer le réseau, lancer l'installation, clic-clic-clic sur les menus...

C'est très rébarbatif et absolument pas gratifiant techniquement parlant lorsque nous connaissons que trop bien le processus.

Tandis qu'avec Vagrant, voici les étapes à réaliser afin de déployer une machine virtuelle sur mon poste de travail.
Tout passe par la rédaction d'un Vagrant-File qui va permettre de déclarer l'infrastructure que vous souhaitez déployer.

Ici, nous allons déployer une machine virtuelle basée sur l'image ubuntu/trusty64 tout en variabilisant le nombre de coeur vCPUs ainsi que la mémoire RAM de la machine et son hostname dans VirtualBox.

1. Installer Vagrant sur votre machine. (lire la doc)
2. Créer un répertoire de travail.
3. Initialiser ce répertoire auprès de Vagrant avec la commande "vagrant init". En l'état, Vagrant vous créera un Vagrant-File rempli de commentaire afin de vous guider dans la rédaction de celui-ci. L'argument "-m" permet d'avoir un Vagrant-File minimal.
4. Rédiger votre Vagrant-File comme ci-dessous via un éditeur de texte au choix. (Attention ! L'indentation est extrêmement importante, à la manière d'un fichier en .yaml par exemple)
5. Une fois le fichier enregistré, toujours dans le répertoire de travail, utiliser la commande "vagrant validate" afin de vérifier la syntaxe du fichier.
6. Lancer la commande "vagrant up" afin de lancer le déploiement de votre machine virtuelle.
7. Une fois la machine crée, vous pouvez effectuer "vagrant ssh" afin de vous connecter dessus. (en cas de demande de mot de passe, les credentials par défaut sont vagrant/vagrant)
8. Une fois le test de connexion fait et la validation du fonctionnement, vous pouvez forcer une destruction de votre environnement

```shell
~ mkdir lab-1
~ cd lab-1
~/lab-1 vagrant init -m
~/lab-1 VIM Vagrantfile

#Editer le fichier avec l'éditeur de texte de votre préférence, ici VIM#

~/lab-1 vagrant validate
Vagrantfile validated successfully.

~/lab-1 vagrant up

#Le fichier est lu et la machine se crée#

~/lab-1 vagrant ssh

~/lab-1 vagrant destroy -f
```

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

[Lien vers le fichier GitHub](https://github.com/Lucroz94/formations-eazytraining-cursus-devops/blob/main/Vagrant/lab-3/Vagrantfile)


### Description du Vagrant-File

En début de fichier, nous avons l'équivalent du shebang en bash, c'est en Ruby et cela permet de déclarer la suite. C'est obligatoire.

Ensuite, nous annonçons nos variables. Elles peuvent être mises au début comme à la fin, ça n'est pas important, car le Vagrant-File peut récupérer l'information de la variable n'importe où. Je préfère pour un souci pédagogique et de clarté, ainsi que de rapidité si je dois les modifier au besoin, les déclarer au tout début du fichier.

Puis nous avons la partie configuration de l'objet "config".

Cet objet "config" contient l'argument "vm.box" afin de déclarer [l'image à utiliser](https://app.vagrantup.com/ubuntu/boxes/trusty64).

De déclarer le provider de déploiement (ici VirtualBox) avec pour objet "v" (les prochains arguments pour la configuration dans VirtualBox sont raccourcis en "v" mais cela pourrait être n'importe quoi).

v.name = Hostname dans VirtualBox (mais pas celui de la machine à l'intérieur ! Pour cela, un plugin est nécessaire).
v.cpus = La variable de CPU déclarée plus haut, ici, 2 vCPUs.
v.memory = la variable de mémoire RAM déclarée plus haut, ici 2GB de RAM.

Encore une fois, vérifié bien l'indentation afin de correctement clôturer chaque bloc de configuration par un "end" sinon vous aurez une erreur de syntaxe remontée par la commande "vagrant validate".