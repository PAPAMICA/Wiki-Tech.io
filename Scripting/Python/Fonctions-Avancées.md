---
title: Python - Fonctions avancées
description: 
published: true
date: 2021-06-14T08:21:39.720Z
tags: 
editor: markdown
dateCreated: 2021-05-24T16:13:51.003Z
---

![Fichier:Python logo and wordmark.svg — Wikipédia](https://upload.wikimedia.org/wikipedia/commons/thumb/f/f8/Python_logo_and_wordmark.svg/1200px-Python_logo_and_wordmark.svg.png)

# Le constructeur

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

# Le représentateur

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

# Le décorateur

Le décorateur est une fonction permettant de modifier le comportement d'autres fonctions, évitant la répétition de code. Il est appelé par “ @nom\_décoraeur ”.

```python
def title_decorator(function):
    def wrapper(*args, **kwargs):
        print("*"*30)
        print("-"*30)
        function(*args, **kwargs)
        print("-"*30)
        print("*"*30)
    return wrapper

@title_decorator
def title(titre):
    print(titre)

>>> titre = "Ceci est mon texte décoré"
>>> title(titre)
******************************
------------------------------
Ceci est mon texte décoré
------------------------------
******************************
```

Ce décorateur permet simplement de mettre en forme un titre. Mais le décorateur, critiqué pour son nom ne définissant pas assez clairement sa fonction, peut avoir bien d'autres usages.

Par exemple, dans le cadre du _développement web_, les décorateurs sont très utilisés. Typiquement, pour les pages nécessitant une authentification, le décorateur “ @login\_required ” du framework Django est très utile.

```python
from django.contrib.auth.decorators import login_required

@login_required
def my_page(request):
    ...
```

Tout comme “ @login\_required ”, beaucoup de décorateurs sont déjà développés, nécessitant un simple import.