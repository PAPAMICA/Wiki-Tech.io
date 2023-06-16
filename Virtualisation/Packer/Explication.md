---
title: Philosophie derrière Packer
description: 
published: true
date: 2023-06-16T12:05:41.765Z
tags: 
editor: markdown
dateCreated: 2023-06-16T12:01:04.079Z
---

# La philosophie de Packer

Si Hashicorp est devenu une référence dans le développement d'outils liés au **DevOps**, c'est principalement grace à [Terraform](https://terraform.io), [Vault](https://www.vaultproject.io/) ainsi que [Vagrant](https://www.vagrantup.com/) qui sont indiscutablement approuvés dans le monde de la production.

Packer est un peu plus discret que ses frères mais il ne fait pas exception et possède également un certain succès.

Sans réinventer la roue, Packer répond à un besoin très précis : **celui de créer une machine virtuelle à partir de code.**

Dans la pratique, Packer va se charger de piloter un hyperviseur *(ou cloud)* afin de choisir les ressources, la taille de disque et même l'ISO servant à l'installation du système. 

*Si nous sommes dans le cas où notre infrastructure est dans un cloud, Packer peut utiliser l'image préinstallée disponible (AMI pour AWS, Flavor pour OpenStack) pour y ajouter des outils, configuration, agents...*

# Quel objectif à utiliser Packer ?

Il est normal de se demander si notre usage de la virtualisation est bien complémentaire avec Packer. 

À cette question, imaginez le cadre d'un **P**lan de **R**eprise d'**A**ctivité après un incident : Allez-vous installer manuellement chaque machine virtuelle ? Bien sûr que non ! Allez-vous prendre le risque de créer une template à la main et oublier une dépendance cruciale ? Allez-vous réimporter un fichier *qcow2* très ancien car complexe à maintenir ? 

Si vous vous êtes senti visé par l'une de ces questions: Packer est effectivement un outil qui peut vous simplifier la vie.

Celui-ci est facile à maintenir (*code*), automatise la création d'une VM à partir d'une ISO récente qu'il peut lui-même télécharger *(ou non si vous le désirez)* et celui-ci ne possède presque aucun pré-requis technique.
