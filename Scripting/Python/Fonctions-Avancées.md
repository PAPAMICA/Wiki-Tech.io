---
title: Python - Fonctions avancées
description: 
published: true
date: 2022-10-11T14:13:14.207Z
tags: 
editor: markdown
dateCreated: 2021-05-24T16:13:51.003Z
---

![Fichier:Python logo and wordmark.svg — Wikipédia](https://upload.wikimedia.org/wikipedia/commons/thumb/f/f8/Python_logo_and_wordmark.svg/1200px-Python_logo_and_wordmark.svg.png)

# Fonctions
Une fonction (ou méthode), est un bloc d'instructions réutilisable. Si vous venez à réécrire plusieurs fois les mêmes lignes dans votre code, cela signifie que vous pouvez les mettre sous forme de fonction. Votre code sera alors beaucoup plus lisible.

## Déclarer une fonction 
Une fonction se déclare avec le mot-clé "def" et nécessite une identation.
Il suffira ensuite d'appeler la fonction par son nom.
```python
#Déclaration de la fonction
def print_something_useless():
    print("Il fait beau aujourd'hui")
    
#Appel de la fonction
print_something_useless()

>>> Il fait beau aujourd'hui
```

## Les arguments
Une fonction peut demander des arguments. Ce sont des variables à renseigner lors de l'appel de la fonction permettant son execution.
```python
def divide_numbers(dividende,diviseur):
    quotient = dividende/diviseur
    print(quotient)
    
divide_numbers(4,2)

>>> 2
```

Il est possible d'attribuer une valeur par défaut à un argument. Celle-ci sera remplacée si on précise une valeur.

```python
def divide_numbers(dividende,diviseur=3):
    quotient = dividende/diviseur
    print(quotient)
    
divide_numbers(9) #cas 1 : valeur par défaut
divide_numbers(9,9) #cas 2 : nouvelle valeur

>>> 3 #cas 1
>>> 1 #cas 2
```

### Récupérer le résultat d'une fonction
Il est important de noter que toutes les variables d'une focntion sont locales. Une fois l'éxecution de la fonction terminée, toutes ses variables sont effacées. Dans l'exemple précédent il aurait pu être intéressant de récupérer le résultat de la division pour la suite de notre script.
Pour ce faire on utilise le mot-clé ***return***.
Ensuite, on attribue l'appel de la fonction à une ou plusieurs variables.
> Il est possible de retourner plusieurs valeurs
{.is-info}

```python
def operations(nb1,nb2):
    quotient = nb1/nb2
    produit  = nb1*nb2
    somme    = nb1 + nb2
    
    return quotient,produit,somme
    
div, mult, add = operations(4,2)
print("division : " + div)
print("multiplication : " + mult)
print("addition : " + add)

>>> division : 2
>>> multiplication : 8
>>> addition : 6
```

## Fonction "main"
La fonction ***main*** est la fonction principale du script. Elle définit le point d'entrée du script et appelle les autres fonctions. C'est la fonction ***main*** qui va définir le déroulement du script.
Cette fonction spéciale est appelée grâce à un ***if***, qui sera le seul bloc d'instruction hors fonction.
> La fonction *main* ne demande pas d'arguments.
{.is-info}


```python
def print_something_useless():
    print("Il fait beau aujourd'hui")
    
def operations(nb1,nb2):
    quotient = nb1/nb2
    produit  = nb1*nb2
    somme    = nb1 + nb2
    
    return quotient,produit,somme

def main():
    print("Je suis le main")
    print_something_useless()
    div, mult, add = operations(4,2)
    print("division : " + div)
    print("multiplication : " + mult)
    print("addition : " + add)
    print_something_useless()
    
if __name__ == '__main__':
    main()

>>> Je suis le main
>>> Il fait beau aujourd'hui
>>> division : 2
>>> multiplication : 8
>>> addition : 6
>>> Il fait beau aujourd'hui
```
Mais à quoi sert cette fonction ? Tout simplement à pouvoir utiliser les fonctions du script dans un autre script, à l'aide d'un *import*. Si les instructions sont décrites directement à la racine du code, elles seront exécutées dès l'import.
Observons la valeur de \_\_name\_\_ lorsque l'on exécute le script directement :
```python
#./script1.py

print(__name__)

>>> __main__
```
Ici, \_\_name\_\_ est égal à \_\_main\_\_. Maintenant, observons sa valeur lorsque le script est appelé depuis un import :
```python
#./script2.py

import script1

>>> script1
```
Suite à l'import dans *script2*, le print de *script1* s'éxecute puisqu'il est à la racine (tout comme le *if \_\_name\_\_ == '\_\_main\_\_'*). On remarque que cette fois, \_\_name\_\_ est égal à *script1*.
En vérifiant que \_\_name\_\_ est égal à \_\_main\_\_, on s'assure alors que le script est exécuté directement, et non en tant que module, nous permettant de réutiliser nos méthodes dans d'autres scripts.

## Le décorateur

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