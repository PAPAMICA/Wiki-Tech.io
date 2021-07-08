---
title: Ansible - Introduction
description: Présentation et définitions de cette solution
published: true
date: 2021-07-08T16:48:12.037Z
tags: ansible, configuration
editor: markdown
dateCreated: 2021-07-08T16:16:25.971Z
---

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
