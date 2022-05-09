---
title: Vagrant : Comment automatiser rapidement le déploiement de son infrastructure virtuelles en local
description: Vagrant est un outil en ligne de commandes permettant de déployer, à l'aide de plugins, des machines virtuelles en local, sur son poste, via des fichiers de configuration. Il s'appuie pour cela sur VirtualBox, VMWare Workstation etc
published: false
date: 2022-05-09T10:11:58.434Z
tags: linux, windows, macos, automatisation, vagrant, scripting
editor: markdown
dateCreated: 2022-05-08T16:50:46.310Z
---

# Présentation de cas d'utilisations de Vagrant par HashiCorp
![external-content.duckduckgo.com.png](/external-content.duckduckgo.com.png)

## Liens pour Vagrant
1. [Page Web](https://www.vagrantup.com/)
2. [Documentation du projet](https://www.vagrantup.com/docs) 
3. [Vagrant Cloud](https://app.vagrantup.com/boxes/search) (équivalent Docker-Hub)
4. [RubyGem](https://rubygems.org/) (pour les plugins Vagrant directement en Ruby)

> Cette page est rédigée par Lucroz94 [mon GitHub personnel](https://github.com/Lucroz94)
La rédaction est inspirée de la formation suivie sur la plateforme [Eazy-Training](https://eazytraining.fr/cours/vagrant-pour-devops-creez-rapidement-et-facilement-vos-environnements-de-travail/) réalisée par [Dirane Tafen](https://www.linkedin.com/in/dirane-willy-tafen-254913b5/)
{.is-info}

## Présentation de la page

Suite à mon suivi de la formation Vagrant, j'ai compris le potentiel de cet outil et cela a changé ma vision de mes déploiements de VM.
Jusqu'ici, je perdais toujours un temps fou à installer des machines virtuelles en local sur ma machine de travail, faire de la configuration post-installation puis les transformer en enveloppe afin de pouvoir les redéployer plus tard.

En plus du temps que cela prend, cela occupe de l'espace sur mon poste de travail et, en cas de problème sur ma machine, mon travail est perdu.

C'est là où Vagrant intervient, afin de déployer en 5min, post-installation inclus, un environnement complet de travail et tout cela, avec la possibilité de sauvegarder son travail et de laisser disponibles ses VM (ses Vagrant-Box) disponibles sur le Vagrant-Cloud.

L'idée derrière cette page est de vous expliquer, de manière synthétique et avec des cas concrets, quels sont certaines possibilités que peux offrir Vagrant.

Si le sujet vous intéresse, vous pourrez toujours fouiller la documentation qui est très bien rédigée ou acheter une formation sur le sujet afin de l'explorer plus en profondeur.

Mais tout d'abord, présentons l'outil du jour, Vagrant !

## Qu'est-ce que Vagrant ?

Tout d'abord, qu'est-ce que nous dit Internet ?

> "Vagrant is a tool for building and managing virtual machine environments in a single workflow. With an easy-to-use workflow and focus on automation, Vagrant lowers development environment setup time, increases production parity, and makes the "works on my machine" excuse a relic of the past.
> 
> To achieve its magic, Vagrant stands on the shoulders of giants. Machines are provisioned on top of VirtualBox, VMware, AWS, or any other provider. Then, industry-standard provisioning tools such as shell scripts, Chef, or Puppet, can automatically install and configure software on the virtual machine."

**Une brève traduction en Français :**

> Vagrant est un outil en ligne de commande permettant de créer et manager des environnements virtualisés (machines virtuelles) grâce à un processus simplifié (le Vagrant File). L'outil est axé sur l'automatisation et le déploiement rapide d'environnement de développement afin que l'adage "le code fonctionne sur ma machine" ne soit plus qu'une relique du passée.
> 
> Pour ce faire, Vagrant s'appuie sur des providers déjà existant tel que VirtualBox, AWS, VMware, ainsi que d'autres providers et dispose même de la possibilité de créer ses propres providers privés, via une liste de plugins. Il est possible aussi de réaliser des configurations post-installations via des provisionner tel que Puppet/Ansible/Shell script etc...
>
>L'outil est disponible sur Linux/MacOS/Windows.
> Vagrant est développé en Ruby, mais ne nécessite pas de connaissance préalable du langage pour être utilisé et compris.

Conclusion : Pour ceux connaissant Docker et les DockerFile, Vagrant est exactement la même chose, mais pour vos machines virtuelles ! Vous pouvez ainsi, via la création d'un Vagrant File, déclarer tout votre environnement virtuelles des couches les plus basses (combien de CPU/RAM, network, disques à partager...) jusqu'aux couches plus hautes. (installation d'environnement docker, utilisation de playbooks Ansible, création de Vagrant Box permettant de partager sur le Vagrant Cloud ses images...)

Maintenant, passons à la pratique avec deux cas concrets d'utilisation plus ou moins complexe !

## Déploiement d'une machine virtuelle

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

Puis nous avons la partie configuration de l'objet "config"

Cet objet "config" contient l'argument "vm.box" afin de déclarer [l'image à utiliser](https://app.vagrantup.com/ubuntu/boxes/trusty64)

De déclarer le provider de déploiement (ici VirtualBox) avec pour objet "v" (les prochains arguments pour la configuration dans VirtualBox sont raccourcis en "v" mais cela pourrait être n'importe quoi) 

v.name = Hostname dans VirtualBox (mais pas celui de la machine à l'intérieur ! Pour cela, un plugin est nécessaire)
v.cpus = La variable de CPU déclarée plus haut, ici, 2 vCPUs
v.memory = la variable de mémoire RAM déclarée plus haut, ici 2GB de RAM

Encore une fois, vérifié bien l'indentation afin de correctement clôturer chaque bloc de configuration par un "end" sinon vous aurez une erreur de syntaxe remontée par la commande "vagrant validate"

## Déploiement d'un cluster web avec un Vagrant-File ainsi qu'un script bash post-installation

Maintenant que vous savez déployer une machine, vous avez envie d'en faire plusieurs !

Imaginons que vous souhaitez déployer plusieurs machines virtuelles afin de tester la fonctionnalité Load-Balancer d'un serveur Web.
De plus, vous avez un joli script Bash que vous souhaitez exécuter à la création de la machine afin d'avoir un environnement ISO-Prod.

Ici, nous allons nous attarder sur un Vagrant-File que j'ai rédigé qui permet de le faire, et je vais, petit à petit, vous expliquer la manière dont certains éléments ont été rajoutés (et d'autres omis, afin que vous puissiez chercher dans la documentation).

La procédure pré-rédaction du Vagrantfile est la même, il faut cependant changer de répertoire de travail.

```ruby
# -*- mode: ruby -*-
# vi: set ft=ruby :

RAM = 1024
CPU = 1
Private_IP_LB = "192.168.56.140"
Private_IP_WEB1 = "192.168.56.141"
Private_IP_WEB2 = "192.168.56.142"

Vagrant.configure("2") do |config|
  #Configure Load-Balancer VM
  config.vm.define "lb", primary: true do |lb|
    lb.vm.box = "debian/bullseye64"
    lb.vm.hostname = "LB"
    lb.vm.provider "virtualbox" do |v|
      v.name = "LB"
      v.cpus = CPU
      v.memory = RAM
    lb.vm.network "private_network", ip: Private_IP_LB
    lb.vm.provision "shell",
      inline: "apt update && apt install -y curl && curl -Ls https://raw.githubusercontent.com/Lucroz94/terminal/main/server_utils.sh | bash -s -- --verbose --motd --all-users"
    end
  end
  #Configure first web-server VM
  config.vm.define "web1" do |web1|
    web1.vm.box = "debian/bullseye64"
    web1.vm.hostname = "WEB1"
    web1.vm.provider "virtualbox" do |v|
      v.name = "WEB1"
      v.cpus = CPU
      v.memory = RAM
    web1.vm.network "private_network", ip: Private_IP_WEB1
    web1.vm.provision "shell",
      inline: "apt update && apt install -y curl && curl -Ls https://raw.githubusercontent.com/Lucroz94/terminal/main/server_utils.sh | bash -s -- --verbose --motd --all-users"
    end
  end
  #Configure second web-server VM
  config.vm.define "web2" do |web2|
    web2.vm.box = "debian/bullseye64"
    web2.vm.hostname = "WEB2"
    web2.vm.provider "virtualbox" do |v|
      v.name = "WEB2"
      v.cpus = CPU
      v.memory = RAM
    web2.vm.network "private_network", ip: Private_IP_WEB2
    web2.vm.provision "shell",
      inline: "apt update && apt install -y curl && curl -Ls https://raw.githubusercontent.com/Lucroz94/terminal/main/server_utils.sh | bash -s -- --verbose --motd --all-users"
    end
  end
end
```
### Description du Vagrant-File

Alors, nous avons ici rajouté pas mal d'éléments.

Tout d'abord, nous avons rajouté la mention "vm.network" ainsi que l'IP fixe de la machine, variabilisé.
Vous pouvez très bien laisser le type en DHCP, je vous laisse consulter la documentation pour cela.

```shell
Private_IP_WEB2 = "192.168.56.142"

web2.vm.network "private_network", ip: Private_IP_WEB2
```

Nous avons aussi l'argument 

```shell
"vm.provision" "shell",
	inline "la commande a exécuté"
```
  
Celui-ci permet de dire à Vagrant de lancer une commande Bash une fois la création de la VM terminer afin d'exécuter du code dedans.
Ici, il est question d'un script Bash que j'ai modifié pour mes besoins afin d'installer des utilitaires que j'apprécie sur mes machines, disponible [ici](https://github.com/Lucroz94/terminal/blob/main/server_utils.sh)

Nous avons aussi l'utilisation d'un plugin, nommé [Hostsupdater](https://github.com/agiledivider/vagrant-hostsupdater) à installer au préalable (pour celui-ci, cadeau, car il n'est plus maintenu et un bug empêche l'installation depuis Vagrant sans un petit workaround : [lien](https://github.com/hashicorp/vagrant/issues/8785))

Ce plugin, permet, parmi pleins d'autres options, de lire et d'écrire dans le fichier host de la machine hôte et invité afin, une fois sa création réalisée, d'appliquer un hostname précis dans la machine invité afin de faciliter les translations DNS.

L'argument est
```shell
v.name = "WEB2" 
```

Et comme vous pouvez le constater, il n'y a pas de tâches d'installation d'un serveur Web, car ceci nécessite de fouiner dans la documentation afin d'ajouter les arguments supplémentaires, et je vous laisse ce petit plaisir !


## En conclusion

Avec ces quelques éléments d'applications concrets, vous pouvez vous donner une première idée de ce qu'est Vagrant, son intérêt et ses possibilités.

Cette rédaction a été volontairement réduite afin d'éveiller votre curiosité et de vous inciter à lire la documentation très bien fournie du projet afin de, par exemple, comprendre ce qu'est le Vagrant Cloud et comment l'exploiter, récupérer des Vagrant Box ou uploader les vôtres, provisionner vos VM par AWS, exploiter des plugins crées par la communauté...

Je remercie encore [Dirane Tafen](https://www.linkedin.com/in/dirane-willy-tafen-254913b5/) ainsi que l'équipe de [Eazy-Training](https://eazytraining.fr/cours/vagrant-pour-devops-creez-rapidement-et-facilement-vos-environnements-de-travail/) pour cette formation très complète sur Vagrant.

Si vous souhaitez voir des cas d'utilisations progressifs de Vagrant, je mets à disposition mon [GitHub](https://github.com/Lucroz94/formations-eazytraining-cursus-devops) où je compte publier au fur et à mesure de mon apprentissage de l'outil de nouveau Vagrantfile et VagrantBox afin de déployer des architectures de plus en plus complexe.

Merci d'avoir lu jusqu'au bout et n'hésitez pas à me contacter par mail : lucroz@lucroz.fr ou par Discord: Lucroz94#1579 si vous souhaitez échanger sur le sujet ! A bientôt pour de prochains articles !