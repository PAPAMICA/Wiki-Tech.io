---
title: Vagrant : Comment automatiser le déploiement de son infrastructure virtuelles en local
description: Vagrant est un outil en ligne de commandes permettant de déployer, à l'aide de plugins, des machines virtuelles en local, sur son poste, via des fichiers de configuration. Il s'appuie pour cela sur VirtualBox, VMWare Workstation etc
published: false
date: 2022-05-08T16:50:46.310Z
tags: linux, windows, macos, automatisation, vagrant, scripting
editor: markdown
dateCreated: 2022-05-08T16:50:46.310Z
---

# Présentation de cas d'utilisations de Vagrant WIP
![external-content.duckduckgo.com.png](/external-content.duckduckgo.com.png)

## Liens pour Vagrant
1. Page Web : https://www.vagrantup.com/
2. Documentation du projet : https://www.vagrantup.com/docs
3. Vagrant Cloud (équivalent Docker-Hub) : https://app.vagrantup.com/boxes/search

> Cette page est rédigé par Lucroz94 (https://github.com/Lucroz94)
La rédaction est inspiré de la formation suivie sur la plateforme Eazy-Training : https://eazytraining.fr/cours/vagrant-pour-devops-creez-rapidement-et-facilement-vos-environnements-de-travail/ réalisé par Dirane Tafen : https://www.linkedin.com/in/dirane-willy-tafen-254913b5/
{.is-info}

## Présentation de la page

Suite à mon suivi de la formation Vagrant, j'ai compris le potentiel de cet outil et cela a changé ma vision de mes déploiements de VM.
Jusqu'ici, je perdais toujours un temps fou à installer des machines virtuelles en local sur ma machine de travail, faire de la configuration post-installation puis les transformer en enveloppe afin de pouvoir les redéployer plus tard.

En plus du temps que cela prends, cela occupe de l'espace sur mon poste de travail et, en cas de problème sur ma machine, mon travail est perdu.

C'est là où Vagrant interviens, afin de déployer en 5min, post-installation inclus, un environnement complet de travail et tout cela, avec la possibilité de sauvegarder son travail et de laisser disponible ses VM (ses Vagrant-Box) disponible sur le Vagrant-Cloud.

L'idée derrière cette page est de vous expliquer, de manière synthétique et avec des cas concret, quels sont les possibilités que peux offrir Vagrant.

Si le sujet vous intéresse, vous pourrez toujours fouiller la documentation qui est très bien rédigé ou acheter une formation sur le sujet afin de l'explorer plus en profondeur.

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
> Vagrant est développé en Ruby mais ne nécessite pas de connaissance préalable du langage pour être utilisé et compris.

Conclusion : Pour ceux connaissant Docker et les DockerFile, Vagrant est exactement la même chose mais pour vos machines virtuelles ! Vous pouvez ainsi, via la création d'un Vagrant File, déclarer tout votre environnement virtuelles des couches les plus basses (combien de CPU/RAM, network, disques à partager...) jusqu'aux couches plus hautes (installation d'environnement docker, utilisation de playbooks Ansible, création de Vagrant Box permettant de partager sur le Vagrant Cloud ses images...)

Maintenant, passons à la pratique avec quelques cas concret d'utilisation plus ou moins complexe !

## Déploiement d'une machine virtuelle

Imaginons que vous souhaitez tester une application sur une machine virtuelle.
Pour ce faire, vous devez ouvrir votre hyperviseur (VMware Workstation, VirtualBox...), cliquer sur une interface graphique afin de choisir votre ISO, vos ressources compute (CPU/RAM/DISK), préparer le réseau, lancer l'installation, clic-clic-clic sur les menus...

C'est très rébarbatif et absolument pas gratifiant techiquement parlant lorsque nous connaissons que trop bien le processus.

Tandis qu'avec Vagrant, voici les étapes à réaliser afin de déployer une machine virtuelle sur mon poste de travail.
Tout passe par la rédaction d'un Vagrant-File qui va permettre de déclarer l'infrastructure que vous souhaitez déployer.

Ici, nous allons déployer une machine virtuelle baser sur l'image ubuntu/trusty64 tout en variabilisant le nombre de coeur CPU ainsi que la mémoire RAM de la machine ainsi que son hostname dans VirtualBox

1. Installer Vagrant sur votre machine (lire la doc)
2. Créer un répertoire de travail
3. Initialiser ce répertoire auprès de Vagrant avec la commande "vagrant init". En l'état, Vagrant vous créera un Vagrant-File rempli de commentaire afin de vous guider dans la rédaction de celui-ci. L'argument "-m" permet d'avoir un Vagrant-File minimal.
4. Rédiger votre Vagrant-File comme ceci

![vagrant-file-ubuntu.png](/vagrant-file-ubuntu.png)

(lien vers le fichier GitHub : https://github.com/Lucroz94/formations-eazytraining-cursus-devops/blob/main/Vagrant/lab-3/Vagrantfile)

### Description du Vagrant-File

En début de fichier, nous avons l'équivalent du shebang en bash, c'est en Ruby et cela permet de déclarer la suite. C'est obligatoire.

Ensuite, nous annonçons nos variables. Elles peuvent être mise au début comme à la fin, ça n'est pas important car le Vagrant-File peux récupérer l'information de la variable n'importe où. Je préfère pour un souci pédagogique et de clarté, ainsi que de rapidité si je dois les modifier au besoin, les déclarer au tout début du fichier.

Puis nous avons la partie configuration de l'objet "config"

Cet objet "config" à part la suite les arguments "vm.box" afin de déclarer l'image à utiliser (celle-ci en l'occurence : https://app.vagrantup.com/ubuntu/boxes/trusty64)

De déclarer le provider de déploiement (ici VirtualBox) avec pour objet "v" (les prochains arguments pour la configuration dans VirtualBox est raccourcis en "v" mais cela pourrait être n'importe quoi) 

v.name = Hostname dans VirtualBox (mais pas celui de la machine à l'intérieur ! Pour cela, un plugin est nécessaire)
v.cpus = La variable de CPU déclarée plus haut, ici, 2 CPU
v.memory = la variable de mémoire vive déclarée plus haut, ici 2Gb de RAM

## Déploiement d'un cluster web avec un Vagrant-File ainsi qu'un script bash post-installation

Imaginons que vous souhaitez déployer plusieurs machines virtuelles afin de tester la fonctionnalité Load-Balancer d'un serveur Web.
De plus, vous avez un joli script Bash que vous souhaitez éxecuter à la création de la machine afin d'avoir un environnement ISO-Prod.

Ici, nous allons nous attarder sur un Vagrant-File que j'ai rédigé qui permet de le faire, et je vais, petit à petit, vous expliquer la manière dont cela est construit.

