---
title: Python -  Conditions et boucles 
description: IF, ELSE, ELIF, WHILE, Comparaisons, etc…
published: true
date: 2021-11-17T12:58:56.851Z
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

## WHILE

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

## FOR

La boucle for permet de parcourir un élément. En python, la boucle for est plutôt bien faite et assez simple à utiliser. On attribue à une variable la valeur de l'item courant de l'élément, qui changera donc à chaque tour de boucle. 
Syntaxe :
```python
for variable in element:
  instruction1
  instruction2
  ...
```

Par exemple, si on veut parcourir une liste :
```python
cars = ["Tesla","Mercedes","BMW"]
for car in cars: 
    print(car)
    
    """
    Attention : 'car' est la variable que l'on vient de déclarer
    qui prend la valeur de l'item actuel de la liste 'cars'
    """

Résultat :
>>> "Tesla"
>>> "Mercedes"
>>> "BMW"
```

### La syntaxe "in range"
La boucle for peut aussi être utilisée pour exécuter une action un nombre défini de fois. Au lieu d'utiliser une boucle while en définissant une variable (telle que i=0) et de devoir l'incrémenter en fin de boucle, on peut utiliser la fonction "range".

Fonctionnement de la fonction "range" :
```python
range(début,fin,step)
```
|-
| Début | (optionnel) valeur de début de la plage de valeurs. Par défaut défini à 0 si non précisé
| Fin |  (requis) valeur de fin de la plage de valeurs. Attention : cette valeur est exclue
| Step |(optionnel) Pas d'incrément. Par défaut défini à 1 si non précisé

### Utilisation dans la boucle FOR
```python
for i in range(2,8,2):
    print(i)
    
Résultat :
>>> 2
>>> 4 
>>> 6
```

## Les instructions Break et Continue
### Break
L'instruction "break" permet de stopper une boucle en cours d'exécution. Par exemple, si vous souhaitez parcourir une liste jusqu'à tomber sur une valeur précise, vous pouvez interrompre la boucle.

Exemple :
```python
liste = [1,9,0,3,6]

for i in liste:
    print(i)
    if i == 0:
        print("j'aime pas les 0, j'arrête")
        break
        
Résultat :
>>> 1
>>> 9
>>> 0
>>> "j'aime pas les 0, j'arrête"
```

### Continue
L'instruction "continue" permet de relancer la boucle sans exécuter les instructions suivantes. Cela peut être utilse pour ignorer un élément. Dans notre exemple précédent, nous aurions bien voulu aficher les chiffres après 0.

Exemple:
```python
liste = [1,9,0,3,6]

for i in liste:
    if i == 0:
        continue
    print(i)
        
Résultat :
>>> 1
>>> 9
>>> 3
>>> 6
```