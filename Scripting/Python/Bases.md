---
title: Python - Présentation et Installation
description: Découvrir Python et l’installer !
published: true
date: 2021-06-14T08:19:23.471Z
tags: 
editor: markdown
dateCreated: 2021-05-24T16:10:06.281Z
---

![Fichier:Python logo and wordmark.svg — Wikipédia](https://upload.wikimedia.org/wikipedia/commons/thumb/f/f8/Python_logo_and_wordmark.svg/1200px-Python_logo_and_wordmark.svg.png)

# Algébrie

| **Opérateur** | **Usage** |
| --- | --- |
| **+** | Additionne deux nombres |
| **\-** | Soustrait deux nombres |
| **\*** | Multiplie deux nombres |
| **/** | Divise deux nombres |
| **//** | Retourne le nombre entier d'une division |
| **%** | Retourne le reste d'une division |

*Exemple :*

```python
>>> 2+3
# Le résultat sera 5
>>> 3*3
# Le résultat sera 9
```

# Les variables

## Les bases

-   Déclaration d'une variable :

```python
nom_variable = valeur
```

> 1.  Le nom de la variable ne peut être composé que de lettres, majuscules ou minuscules, de chiffres et du symbole souligné « \_ » (appelé underscore en anglais).
> 2.  Le nom de la variable ne peut pas commencer par un chiffre.  
> 3.  Le langage Python est sensible à la casse, ce qui signifie que des lettres majuscules et minuscules ne constituent pas la même variable (la variable AGE est différente de aGe, elle-même différente de age).
{.is-info}

Il existe 2 conventions principales pour nommer une variable :

1.  On remplace les espaces par un **\_** : `mon_age`
2.  On écrit le début de chaque mot, sauf le premier, avec une majuscule : `monAge`  
    Pour affecter une valeur à cette variable :

```python
monAge = 19
```

On peut interagir avec cette variable en modifiant son contenu comme bon nous semble :

```python
monAge = 19
# monAge vaut 19
monAge = monAge + 4
# monAge vaut 23
monAge = monAge * 2
# monAge vaut 46
```

## Différents types de données

| **Type de donnée** | **Commentaire** |
| --- | --- |
| Nombres entiers (int) | Nombre sans virgule : `3` |
| Nombres flottants (float) | Nombre avec virgule : `3.2442` |
| Chaine de caractères | Contient des lettres, des phrases : `Bonjour !` |

Pour les chaines de caractères, il faut faire attention aux types de guillements utilisés :

```python
maPhrase = 'J'aime le python'
# Syntaxe incorrecte !
# Pour corriger cela, il faut échapper le ' :
maPhrase = 'J\'aime le python'
# Affiche bien la phrase

maPhrase = "j'aime le python"
# J'aime le python

maPhrase = """j'aime
              le
              python"""
# Les trois guillemets permettent de ne pas avoir à échapper les caractères, et permettent de faire un retour à la ligne sans utiliser \n
```

## Trucs et astuces

### Incrémenter des variables

Lorsqu'il s'agit d'incrémenter des variables, on peut faire de plusieurs façons :

```python
# Méthode 1 : Rajouter la valeur à la variable
maVariable = 1
maVariable = maVariable + 1
# Méthode 2 : Incrémenter directement la variable
maVariable = 1
maVariable += 1
```

### Permutation de variable

```python
>>> a = 5
>>> b = 32
>>> a,b = b,a # permutation
>>> a
32
>>> b
5
>>>
```

### Affecter la même valeur à plusieurs variables

```python
>>> x = y = 3
>>> x
3
>>> y
3
>>>
```

# Les fonctions

Syntaxe d'une fonction :

```python
nom_de_la_fonction(parametre_1,parametre_2,…,parametre_n)
```

## Fonctions de base

-   La fonction **type**  
    Cette fonction permet de retourner le type d'une variable.

```python
a=3
type(a)
# retourne <class 'int'>
```

-   La fonction **print**  
    Cette fonction ne sert uniquement qu'à afficher du texte ou une variable. Contrairement à la console qui est capable d'afficher une variable sans avoir à appeler cette fonction, sur un **IDE** vous serez obligé de vous en servir.

```python
>>> a = 3
>>> print(a)
>>> a = a + 3
>>> b = a - 2
>>> print("a =", a, "et b =", b)
```

-   La fonction **input**  
    Cette fonction permet de récupérer la saisie d'un utilisateur.

```python
choix = input("Faite votre choix : ")
```

Par défaut, la valeur sera une chaine de caractères. Pour la convertir en entier, on peut utiliser ceci :

```python
annee = input("Saisir une annee : ")
```