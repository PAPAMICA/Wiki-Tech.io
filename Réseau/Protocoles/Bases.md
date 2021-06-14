---
title: Les bases
description: Les bases du réseaux.
published: true
date: 2021-06-14T08:02:01.839Z
tags: 
editor: markdown
dateCreated: 2021-05-24T10:36:32.269Z
---

# Les adresses IP

### Qu’est-ce qu’une adresse IP ?

Une adresse IP est une double adresse.  
Elle identifie à la fois **un réseau** ET **l’adresse d’une machine dans ce réseau** (ou de l’interface d’une machine pour être plus précis)

Mais cette adresse seule ne vaut rien !  
Car sans le masque, vous ne saurez jamais distinguer la partie réseau et la partie machine de l’adresse.

L'adresse IP est donc toujours associée à un masque.

### Codage de l’adresse IP

L’adresse IP est codée sur 4 octets.  
Elle s’écrit en décimal, en séparant chaque octet par des points, par exemple:  
192.168.0.1

Comme nous l’avons dit précédemment, cette adresse seule ne sert à rien et ne nous donne pas d’information sur l’adresse du réseau ou de la machine.  
Il faut lui associer le masque…

---

# Les masques de sous réseau

### Qu’est-ce qu’un masque de sous-réseau ?

Un masque de sous réseau est **un séparateur** qui permet de distinguer les parties **réseau** et **machine** d’une adresse IP.

Les bits positionnés à 1 dans le masque indiquent la partie réseau de l'adresse.

Par exemple, si j’associe l’adresse IP et le masque suivants:  
192.168.0.1  
255.255.255.0

Je peux dire que les trois premiers octets de l’adresse représentent la partie réseau de l’adresse.  
En effet, si je transforme le masque en binaire, j’obtiens:  
255.255.255.0 = 11111111.11111111.11111111.00000000

Nous voyons bien que les trois premiers octets ne contiennent que des 1, alors que le dernier octet ne contient que des 0.  
La définition du masque nous dit donc que les trois premiers octets de l’adresse identifient le réseau.

La partie réseau de l’adresse est donc:  
192.168.0  
Et la partie machine de l’adresse au sein de ce réseau est donc représentée par le quatrième octet:  
1

### Quand les masques deviennent méchants

Ici, c’est encore facile, mais cela se corse dès que l’on a des masques pour lesquels la séparation se fait en plein milieu d’un octet.  
Prenons par exemple l’adresse précédente, mais associée à un masque plus fun:  
192.168.0.1  
255.224.0.0

![:waw:](https://i2.wp.com/openclassrooms.com/bundles/common/images/smiley/waw.png?w=920&ssl=1)

Là cela se corse et il faut obligatoirement revenir en binaire pour savoir où se fait la séparation:  
255.224.0.0 = 11111111.11100000.00000000.00000000

Argh, comme prévu la séparation se fait au milieu d’un octet.

**Comment faire alors pour trouver l’adresse du réseau ?**

Il faut là encore, comme le masque, l’écrire en binaire.  
192.168.0.1 = 11000000.10101000.00000000.00000001  
Que nous associons au masque:  
255.224.0.0 = 11111111.11100000.00000000.00000000

Ce qui nous donne pour la séparation dans l’adresse:  
192.168.0.1 = 11000000.10101000.00000000.00000001

**Mais comment écrire une adresse de réseau dont un octet ne contient que 3 bits ?**

Et bien nous ne pouvons pas l’écrire en décimal. C’est mort. Foutu. 

![:(](https://i2.wp.com/openclassrooms.com/bundles/common/images/smiley/triste.png?w=920&ssl=1)

Mais ce n’est pas grave, car on ne nous demande jamais de donner la partie réseau de l’adresse, mais plutôt l’adresse du réseau.

---

# L’adresse du réseau

### Qu’est-ce que l’adresse du réseau ?

L’adresse du réseau est la première adresse d’une plage d’adresse.  
Comme nous avons dit qu’une adresse et un masque définissaient une plage d’adresses, il ne nous reste plus qu’à trouver la première.

Pour la trouver, rien de plus simple, vu que c’est la première, c’est l’adresse la plus basse, donc c’est l’adresse qui ne contient que des 0.

C'est donc l'adresse 0.0.0.0 ?

Meuhnon !  
C’est l’adresse qui ne contient que des 0 **dans la partie machine de l’adresse**, pas dans la partie réseau.

Si nous reprenons l’exemple précédent:  
192.168.0.1 = 11000000.10101000.00000000.00000001

Nous pouvons passer tous les bits de la partie machine à 0:  
11000000.10100000.00000000.00000000  
Ce qui vaut en décimal:  
192.160.0.0

L’adresse du réseau défini par le couple 192.168.0.1/255.224.0.0 est donc **192.160.0.0**.

Nous avons trouvé la première adresse de la plage d’adresse, qui est l’adresse du réseau.  
Nous aimerions maintenant trouver la dernière adresse de la plage pour ainsi connaître toute la plage.  
On appelle cette adresse **l’adresse de broadcast**.

---

# L’adresse de broadcast

### Qu’est-ce que l’adresse de broadcast ?

L’adresse de broadcast est l’adresse qui permet de s’adresser à toutes les adresses d’un réseau à la fois.

Elle est définie comme étant **la dernière adresse** d’une plage d’adresses réseau.

**Comment définir la dernière adresse d’une plage d’adresses ?**

Et bien comme nous avions mis des 0 pour trouver l’adresse la plus petite, nous n’allons mettre que des 1 dans la partie machine de l’adresse pour trouver l’adresse de broadcast.

192.168.0.1 = 11000000.10101000.00000000.00000001  
On remplace la partie machine par des 1, ce qui donne:  
11000000.10111111.11111111.11111111  
Ce qui vaut en décimal:  
192.191.255.255

La plage d’adresses réseau définie par le couple 192.168.0.1/255.224.0.0 s’étend donc de 192.160.0.0 à 192.191.255.255.

Super… mais bon, j’ai fait tous les calculs binaires en un clin d’œil alors que ceux-ci ne sont pas si simples à faire normalement… 

![o_O](https://i1.wp.com/openclassrooms.com/bundles/common/images/smiley/blink.gif?w=920&ssl=1)

  
Ça serait bien d’avoir une méthode plus rapide pour faire les calculs.

C’est là qu’entre en jeu [**la méthode magique**](https://labo-tech.fr/base-de-connaissance/comment-calculer-les-masques/) !