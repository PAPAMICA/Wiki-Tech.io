---
title: Threading
description: Paralléliser les opérations
published: true
date: 2022-10-11T09:37:14.419Z
tags: 
editor: markdown
dateCreated: 2022-10-11T09:10:42.435Z
---

![Fichier:Python logo and wordmark.svg — Wikipédia](https://upload.wikimedia.org/wikipedia/commons/thumb/f/f8/Python_logo_and_wordmark.svg/1200px-Python_logo_and_wordmark.svg.png)

# Threading
## Exécuter plusiuers fonctions en même temps
Certaines fonctions d'un programme peuvent prendre du temps à s'exécuter. Cependant, vous ne souhaitez pas forcément que tout le programme s'arrête pour cette fonction. Pour une interface graphique par exemple ! Si une action requiert un certain temps, vous ne souhaiterez certainement pas planter toute l'interface. C'est là que le multi-threading intervient.

Prenons l'exemple suivant :
```python
def wait(duration):
    time.sleep(duration)
    print("j'ai attendu")

def hello():
    print("Hello")

wait(5)
hello()

>>> j'ai attendu
>>> Hello
```
Dans ce cas, on doit attendre que la fonction "wait" ait terminé son exécution pour passer à la suite. On va donc créer des threads :
```python
import threading
import time

def wait(duration):
    time.sleep(duration)
    print("j'ai attendu")

def hello():
    print("Hello")

wait = threading.Thread(target=wait, args=(2,))
hello = threading.Thread(target=hello)

wait.start()
hello.start()

>>> Hello
>>> j'ai attendu
```
Ici, on voit que la fonction "hello" n'a pas attendu la fin de "wait" pour se lancer.
Pour créer le thread, on appelle la fonction *Thread* de la library *threading* (en l'affectant à une variable) avec les arguments suivants :
- target : le nom de la fonction à exécuter
- args (optionnel) : si la fonction a besoin d'arguments, on les donne ici
Ensuite, on lance le thread avec la fonciton *start*
> **Attention :** J'attire votre attention sur la virgule après le *2* dans les args. En effet, un tuple est attendu. Si vous n'avez qu'un seul argument à donner, il faut donc mettre une virgule pour indiquer qu'il s'agit d'un tuple.
{.is-warning}

## Attendre la fin de l'exécution d'un thread
Si vous ne voulez pas bloquer tout le programme pour une fonciton, il peut tout de même y avoir des dépendances. Il faudra donc préciser que le thread doit être terminé avant de continuer le script à l'aide de la fonction *join*.
Dans cet exemple, la fonction *end* doit être executée en dernier. On indique alors que les threads doivent avoir terminé leur exécution :
```python
import threading
import time

def wait(duration):
    time.sleep(duration)
    print("j'ai attendu")

def hello():
    print("Hello")
    
def end():
    print("execution terminée")

wait = threading.Thread(target=wait, args=(2,))
hello = threading.Thread(target=hello)

wait.start()
hello.start()

wait.join()
print_stg.join()

end()

>>> Hello
>>> j'ai attendu
>>> execution terminée
```