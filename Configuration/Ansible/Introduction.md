---
title: Ansible - Introduction
description: Présentation et définitions de cette solution
published: true
date: 2021-07-08T16:55:49.993Z
tags: ansible, configuration
editor: markdown
dateCreated: 2021-07-08T16:16:25.971Z
---

![](https://blog.dbi-services.com/wp-insides/uploads/sites/2/2021/03/ansible-logo.png)

# Introduction et présentation

<div class="video-responsive">
<iframe width="560" height="315" src="https://www.youtube.com/embed/Cisg9bLhLkk" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
</div>

## Informations utiles
- Créé en 2012 (2015 repris par Redhat) par Michael DeHaan (Cobler, outil de provisionnement)
- Ansible = Infrastructure as code + déploiement de configurations + installations
- à base de python
- Documentation : https://docs.ansible.com/
- orchestrateur basé sur du push > pas d'agent = serveur distant pousse les informations à la différence des outils à base d'agents > pull (puppet etc..)

## Concurrents
* puppet
* chef
* saltstack
* capistrano

## Avantages
- simplicité lié à l'utilisation de SSH
- intégration facile dans les outils de CI/CD
- facilité d'utilisation à base de fichiers yaml
- de très nombreux modules et une très forte communauté (notamment via ansible galaxy)
- différentes notions et déinitions : inventory + playbook + rôles (inventory > playbook < rôles)
- système de templating = jinja2 (python) (équivalent à erb pour puppet (ruby))
- également utilisable pour récupérer les données de vos serveurs

## Outils
* ansible vault
* ansible playbook
* ansible galaxy
* ansible doc

## Types d'installation
* via les sources
* via les paquets
* via librairie python (pip)

## Exemples de modules
* postgres
* vmware
* aws
* libvirt (kvm)
* network
* grafana
* postgresql
* mysql
...

# Notions et définitions
<div class="video-responsive">
<iframe width="560" height="315" src="https://www.youtube.com/embed/tirjpYSMkkM" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
</div>

## Control node
* noeud disposant de ansible et permettant de déployer
* accès ssh aux autres machines (bastions...)
* password ou clef ssh
* sécurité importante

## Managed nodes
* serveurs cibles
* permet la connexion ssh
* élévation de privilèges via le user

## Inventory
* inventaires des machines  (ip, dns)
* format ini (plat) ou format yaml
* et les variables (host_vars et group_vars)
* statique (fichiers) ou dynamique (api via script)
* utilisation de patterns possible (srv-pg93-0[0-2])

## Groups
* dans un inventaire les machines peuvent être regroupées (serveur web, databases...)
* possibilité de créer différents niveaux > arbre (parents/enfants)
* groupe racine = all

## Groups Vars
* variables d'un même groupe
* définie dans le fichier central d'inventory
* ou dans un répertoire spécifique (reconnu par ansible)

## Host Vars
* variables spécifiques à un serveur en particulier
* surcharge d'autres variables définies plus haut dans l'arbre - ex - groupe

## Tasks
* actions variées (user, group, command, module)
* format yaml

## Modules
* ensemble d'actions ciblées sur une utilisation commune
* pour un outil donnée : ex. postgres, mysql, vmware...
* chacune de ses actions est utilisable via une task
* chaque action prend des options
* les actions peuvent fournir un retour (id, résultat...)
* fournis par ansible pour l'essentiel
* peuvent être chargés spécifiquement
* contribution possible auprès des mainteneurs

## Rôles
* ensemble d'actions coordonnées pour réaliser un ensemble cohérent (installer nginx et le configurer etc)
* organisé en différents outils (tasks, templates, handlers, variables (default ou non), meta)
* peuvent être partagés sur le hub ansible galaxy
* il vaut mieux les versionner

## Playbooks
* un fichier (et rien d'autres...)
* applique des rôles à un inventory
* partie cruciale inventory > playbook < rôles
* peut contenir des variables (à éviter)
* peut contenir des tasks (à éviter)
* peut contenir des conditions (à éviter)

## Plugins
* modifie ou augmente les capacités de ansible
* de différentes manières : output, inventory dynamique, strategy, tests...


