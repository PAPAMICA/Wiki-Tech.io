---
title: Python - Programmation Orientée Objet (POO) 
description: Comprendre la notion de programmation orientée objet
published: true
date: 2021-11-17T09:57:19.118Z
tags: 
editor: markdown
dateCreated: 2021-11-17T09:11:34.380Z
---

![Fichier:Python logo and wordmark.svg — Wikipédia](https://upload.wikimedia.org/wikipedia/commons/thumb/f/f8/Python_logo_and_wordmark.svg/1200px-Python_logo_and_wordmark.svg.png)

# Concept de programmation orientée objet (POO)

Dans la programmation procédurale, comme le C, une suite de fonctions (appelées aussi procédures ou routines) est définie, fonctions pouvant être appelées dans l'ordre que l'on souhaite pour traiter des données en local.

Contrairement à la programmation procédurale, la POO (OOP en anglais) ne définit pas de procédures. Elle construit des objets pour les faire interagir entre eux. Un objet est défini par une classe (une catégorie), des variables d'instance (attributs), et des méthodes (fonctions).

Par exemple, si nous construisons une voiture nous pouvons la définir comme suit :

**Classe (catégorie générale)** : Véhicules roulants

**Attributs :**
- Type de véhicule : voiture
- Nombre de roues : 4
- Marque : Tesla
- Modèle : Roadster
- Energie : Electrique

**Méthodes :**
- Rouler
- Freiner
- Détecter un danger
- Servir le café
- Aller dans l'espace

De la même façon, si nous avons construit un objet "maison" avec un garage, nous pourrons les faire interagir entre eux en définissant, par exemple, que le garage reçoit l'information "bip ouverture" et ouvre la porte. L'objet voiture pourra alors avancer (seule puisque c'est une Tesla) à la réception de l'information "pas d'obstacle" (ex : `if front_captor == 0: move(forward)`). Enfin, le garage pourra se fermer après réception du signal "voiture rentrée".
