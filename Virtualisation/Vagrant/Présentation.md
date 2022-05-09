---
title: Présentation de Vagrant by HashiCorp
description: Vagrant est un outil multi-OS en ligne de commande vous permettant de déployer très rapidement, à l'aide d'un fichier de configuration (le Vagrantfile) toute votre infrastructure virtuelle en local. Il est écrit en Ruby et s'appuie sur de nombreux plugins
published: true
date: 2022-05-09T14:03:33.685Z
tags: linux, windows, virtualisation, macos, vagrant, scripting
editor: markdown
dateCreated: 2022-05-09T10:15:14.190Z
---

![external-content.duckduckgo.com.png](/external-content.duckduckgo.com.png)

# Liens pour Vagrant
1. [Page Web](https://www.vagrantup.com/)
2. [Documentation du projet](https://www.vagrantup.com/docs) 
3. [Vagrant Cloud](https://app.vagrantup.com/boxes/search) (équivalent Docker-Hub)
4. [RubyGem](https://rubygems.org/) (pour les plugins Vagrant directement en Ruby)

> Cette page est rédigée par Lucroz94 [mon GitHub personnel](https://github.com/Lucroz94)
La rédaction est inspirée de la formation suivie sur la plateforme [Eazy-Training](https://eazytraining.fr/cours/vagrant-pour-devops-creez-rapidement-et-facilement-vos-environnements-de-travail/) réalisée par [Dirane Tafen](https://www.linkedin.com/in/dirane-willy-tafen-254913b5/)
{.is-info}

# Présentation de la page

Suite à mon suivi de la formation Vagrant, j'ai compris le potentiel de cet outil et cela a changé ma vision de mes déploiements de VM.
Jusqu'ici, je perdais toujours un temps fou à installer des machines virtuelles en local sur ma machine de travail, faire de la configuration post-installation puis les transformer en enveloppe afin de pouvoir les redéployer plus tard.

En plus du temps que cela prend, cela occupe de l'espace sur mon poste de travail et, en cas de problème sur ma machine, mon travail est perdu.

C'est là où Vagrant intervient, afin de déployer en 5min, post-installation inclus, un environnement complet de travail et tout cela, avec la possibilité de sauvegarder son travail et de laisser disponibles ses VM (ses Vagrant-Box) disponibles sur le Vagrant-Cloud.

L'idée derrière cette page est de vous expliquer, de manière synthétique et avec des cas concrets, quelles sont certaines possibilités que peut offrir Vagrant.

Si le sujet vous intéresse, vous pouvez toujours fouiller la documentation qui est très bien rédigée ou acheter une formation sur le sujet afin de l'explorer plus en profondeur.

Mais tout d'abord, présentons l'outil du jour, Vagrant !

# Qu'est-ce que Vagrant ?

Tout d'abord, qu'est-ce que nous dit Internet ?

> "Vagrant is a tool for building and managing virtual machine environments in a single workflow. With an easy-to-use workflow and focus on automation, Vagrant lowers development environment setup time, increases production parity, and makes the "works on my machine" excuse a relic of the past.
> 
> To achieve its magic, Vagrant stands on the shoulders of giants. Machines are provisioned on top of VirtualBox, VMware, AWS, or any other provider. Then, industry-standard provisioning tools such as shell scripts, Chef, or Puppet, can automatically install and configure software on the virtual machine."

**Une brève traduction en Français :**

> Vagrant est un outil en ligne de commande permettant de créer et manager des environnements virtualisés (machines virtuelles) grâce à un processus simplifié (le Vagrant File). L'outil est axé sur l'automatisation et le déploiement rapide d'environnement de développement afin que l'adage "le code fonctionne sur ma machine" ne soit plus qu'une relique du passée.
> 
> Pour ce faire, Vagrant s'appuie sur des providers déjà existants tels que VirtualBox, AWS, VMware, ainsi que d'autres providers et dispose même de la possibilité de créer ses propres providers privés, via une liste de plugins. Il est possible aussi de réaliser des configurations post-installations via des provisionners tels que Puppet/Ansible/Shell script etc...
>
>L'outil est disponible sur Linux/MacOS/Windows.
> Vagrant est développé en Ruby, mais ne nécessite pas de connaissances préalables du langage pour être utilisé et compris.

## Conclusion : 
Pour ceux connaissant Docker et les DockerFile, Vagrant est exactement la même chose, mais pour vos machines virtuelles ! Vous pouvez ainsi, via la création d'un Vagrant File, déclarer tout votre environnement virtuelles des couches les plus basses (combien de CPU/RAM, network, disques à partager...) jusqu'aux couches plus hautes. (installation d'environnement docker, utilisation de playbooks Ansible, création de Vagrant Box permettant de partager sur le Vagrant Cloud ses images...)

Maintenant, passons à la pratique avec deux cas concrets d'utilisation plus ou moins complexe !