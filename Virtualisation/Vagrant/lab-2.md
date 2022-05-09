---
title: Déploiement d'un cluster applicatif
description: Ici, nous allons déployer un cluster de 3 machines en rajoutant de la complexité avec la gestion du network, l'intégration de plugins et l'ajout de commande Bash post-installation
published: true
date: 2022-05-09T12:41:59.191Z
tags: linux, windows, virtualisation, macos, vagrant, scripting
editor: markdown
dateCreated: 2022-05-09T10:29:52.525Z
---

# Déploiement d'un cluster web avec un Vagrant-File ainsi qu'un script bash post-installation

Maintenant que vous savez déployer une machine, vous avez envie d'en faire plusieurs !

Imaginons que vous souhaitez déployer plusieurs machines virtuelles afin de tester la fonctionnalité Load-Balancer d'un serveur Web.
De plus, vous avez un joli script Bash que vous souhaitez exécuter à la création de la machine afin d'avoir un environnement ISO-Prod.

Ici, nous allons nous attarder sur un Vagrantfile que j'ai rédigé qui permet de le faire, et je vais, petit à petit, vous expliquer la manière dont certains éléments ont été rajoutés (et d'autres omis, afin que vous puissiez chercher dans la documentation).

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

Tout d'abord, nous avons rajouté la mention `vm.network` ainsi que l'IP fixe de la machine, variabilisé.
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