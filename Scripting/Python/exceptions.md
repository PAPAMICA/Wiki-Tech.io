---
title: Python - Gérer les erreurs
description: 
published: true
date: 2021-09-02T13:50:16.962Z
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

``a = 1
a/0
Traceback (most recent call last):
  File "<pyshell#1>", line 1, in <module>
    a/0
ZeroDivisionError: division by zero``