---
title: Python -  Conditions et boucles 
description: IF, ELSE, ELIF, WHILE, Comparaisons, etc…
published: true
date: 2021-06-14T08:21:02.249Z
tags: 
editor: markdown
dateCreated: 2021-05-24T16:12:00.664Z
---

![Fichier:Python logo and wordmark.svg — Wikipédia](https://upload.wikimedia.org/wikipedia/commons/thumb/f/f8/Python_logo_and_wordmark.svg/1200px-Python_logo_and_wordmark.svg.png)

# Structures conditionnelles

## IF

La structure conditionnelle **IF** permet de faire un **choix**. Cela permet de rendre le code *intelligent*.  
Dans un langage courant, cela donnerait ceci :

```python
Si <réfrigérateur_vide = 1>
  Alors Afficher "Faire des courses"
Fin si
```

En python, cela donnerait :

```python
refrigerateur_vide = 1
if refrigerateur_vide == 1:
  print "Faire des courses"
```

> **Attention :** Dans d'autres langages de programmation tel que le **C#**, la fonction est délimitée par **{}**. En python, la fonction commence par **:** et c'est l'indentation qui délimite.
> Pour éviter les erreurs, les indentations (généralement faites automatiquement) doivent être 4 espaces et non une tabulation.
{.is-warning}

*Démonstration :*

```python
refrigerateur_vide = 1
if refrigerateur_vide == 1: #Je débute le IF
  print "Aller faire des courses" # Je suis dans le IF
  print "Je suis encore de le IF"
  print "Ici aussi"
print "Ici non, mon IF est terminé"
```

## IF ELSE

Je peux également grâce au **ELSE** définir une action si ma condition est fausse.

```python
refrigerateur_vide = 1
if refrigerateur_videv == 1: # Si le frigo est vide, alors ...
  print "Faire des courses"
else: # Sinon ...
  print "Ne pas faire de courses"
```

## ELIF

Cette condition permet d'avoir **plusieurs conditions** dans une clause IF.

```python
refrigerateur_vide = 1
if refrigerateur_vide == 1: # Si le frigo est vide, alors ...
  print "Faire des courses"
elif refrigerateur_vide == 0.5: # Si le frigo est presque vide, alors ...
  print "Faire de petites courses"
else: # Sinon ...
  print "Ne pas faire de courses"
```

## Opérateurs de comparaison

| **Opérateur** | **Signification** |
| --- | --- |
| <   | Strictement inférieur à |
| \>  | Strictement supérieur à |
| <=  | Inférieur ou égal à |
| \>= | Supérieur ou égal à |
| \== | Égal à |
| !=  | Différent de |

> Pour tester l'égalité de deux valeurs, on utilise **\==** et non **\=** qui lui sert à l'affectation de valeurs
{.is-warning}

> Une condition renvoie uniquement deux valeurs : **True** ou **False** (vrai ou faux). Si le test est correcte, il renverra **True**, sinon **False**.
{.is-info}

## Plusieurs conditions pour une clause IF

Si un test à besoin de plusieurs conditions pour être vrai, on peut combiner plusieurs conditions pour une seule clause IF.

```python
permis=False
majeur=True
if permis == True and majeur == True: # Pour que je puisse conduire, il faut que j'ai mon permis et que je sois majeur
  print "Je peux conduire"
else:
  print "Je ne peux pas conduire"
```

Si un test a besoin qu'une condition soit vraie parmi plusieurs, on peut utiliser un “or”.

```python
vote_gagne = False
dictature = True

if (vote_gagne or dictature) == True:
    print("Je dirige le pays")
```

# Les boucles

## La boucle **WHILE**

La boucle **while** (ou tant que) répète une série d'instructions tant qu'une condition n'est pas vraie.  
Syntaxe :

```python
while condition:
  instruction1
  instruction2
  ...
```

*Exemple : Je veux répéter une instruction tant que i n'est pas égal à 10 :*

```python
i = 0
while 1 != 10:
  print ("i ne vaut pas 10")
  i+=1
```

> Il ne faut pas oublier d'incrémenter le compteur, sinon la boucle sera infinie !{.is-warning}