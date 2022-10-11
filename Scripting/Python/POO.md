---
title: Python - Programmation Orientée Objet (POO) 
description: Comprendre la notion de programmation orientée objet
published: true
date: 2022-10-11T14:41:40.154Z
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

# Utilisation des objets
## Déclarer une classe
Une classe est tout simplement déclarée par *"class ClassName:"*, puis les méthodes et attributs associés (indentés). Selon les bonnes pratiques, le nom de la classe doit être attaché, sans tiret, avec une majuscule à chaque mot.
Il est possible de délcarer des attributs de classe :
```python
class Demo:
    var = 1

print(Demo.var)

>>> 1
```
> **Attention** : ici nous parlons bien d'un attribut de **classe** et non d'objet. Pour les attributs d'objet, rendez-vous au chapitre suivant sur le constructeur.
{.is-warning}

## Le constructeur

Un constructeur est une méthode spéciale d'une classe qui permet de construire un objet, de lui donner des attributs.

Le nom de cette méthode est “ \_\_init\_\_ ” et prend en premier argument “ self ”, qui représente l'objet courant, qui est cours de création.

*Exemple : je crée un constructeur permettant de définir une personne* 

```python
class People:
    def __init__(self,firstname,lastname,age):
        self.firstname = firstname
        self.lastname  = lastname
        self.age       = age
```

Notre fonction init demande 3 paramètres, “ self ” étant un paramètre spécial. 

Grâce à ce constructeur il ne reste qu'à créer des objets : 

```python
>>> chuck = People("Chuck","Norris",81)
```

L'objet “chuck” a été créé, on peut désormais accéder à ses différents attributs, les lire et les modifier.

```python
>>> chuck.firstname
'Chuck'
>>> chuck.firstname = "Chucky"
>>> chuck.firstname
'Chucky'
```

## Le représentateur

Reprenons l'exemple précédent. Lorsque l'on souhaite afficher notre objet, la valeur retournée n'est pas tout à fait celle attendue.

```python
>>> chuck
<__main__.People object at 0x03C12820>
```

Nous allons donc utiliser un représentateur pour mettre en forme l'objet.

Cette méthode se définit “ \_\_repr\_\_ ”.

```python
class People:
    def __init__(self,firstname,lastname,age):
        self.firstname = firstname
        self.lastname  = lastname
        self.age       = age

    def __repr__(self):
        return "Nom : %s , Prenom : %s , Age : %s" % (self.lastname, self.firstname, self.age)
```

```python
>>> repr(chuck)
'Nom : Norris , Prenom : Chuck , Age : 81'
```

