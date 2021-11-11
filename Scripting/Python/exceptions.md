---
title: Python - Gérer les erreurs
description: 
published: true
date: 2021-09-02T14:26:10.594Z
tags: 
editor: markdown
dateCreated: 2021-09-02T13:38:45.304Z
---

![Fichier:Python logo and wordmark.svg — Wikipédia](https://upload.wikimedia.org/wikipedia/commons/thumb/f/f8/Python_logo_and_wordmark.svg/1200px-Python_logo_and_wordmark.svg.png)

# Anticiper et gérer les exceptions (erreurs)
## Exceptions par défaut
Lors de l'éxecution d'un script des erreurs peuvent survenir, arrêtant alors le programme. Or, nous ne souhaitons pas forcément l'arrêt du script. Dans certains cas nous savons ce qui peut arriver et nous souhaiterions dérouler jusqu'au bout.
Par chance, python est très bavard !

Prenons l'exemple suivant :
Vous écrivez un script qui réalise une série de calculs avec des nombres donnés. Une division par 0 peut alors arriver. Vous le savez et vous souhaitez simplement être prévenus.

```python
a = 1
a/0
```
Nous obtenons l'erreur :
>Traceback (most recent call last):
  File "<pyshell#1>", line 1, in <module>
    a/0
ZeroDivisionError: division by zero
{.is-danger}
  
Maintenant nous savons que l'erreur corresponsant à une division par zéro s'appelle "ZeroDivisionError". Il est alors possible de l'anticiper grâce à la structure "try: except:"
```python
  a = 0
b = 2
numbers = [a,b]

for nb in numbers:
    try:
        result = 1/nb
        print("nombre = ", nb, "resultat = ", result)
    except ZeroDivisionError:
        print("ca ne fonctionne pas avec ", nb)
  ```
>   ca ne fonctionne pas avec  0
nombre =  2 resultat =  0.5
{.is-success}
  
  Nous constatons que pour le nombre "a" le script n'a pas cessé de fonctionner, et à éxecuté les actions demandées dans le "except"
  
##   Exceptions créées par le développeur
```python
  a = 0
b = 2
numbers = [a,b]

for nb in numbers:
    try:
        result = 1/nb
        if(result < 1):
            raise Exception('LowerOne')
        else:
            print("nombre = ", nb, "resultat = ", result)
        
    except ZeroDivisionError:
        print("ca ne fonctionne pas avec ", nb)
        
    except Exception as error:
        if(str(error) == "LowerOne"):
            print("résultat inférieur à 1")
```
              

                       

