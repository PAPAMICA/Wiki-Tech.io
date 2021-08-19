---
title: Calculer les masques IP
description: Comment calculer ces sal*peries sans internet ?
published: true
date: 2021-06-14T08:09:50.869Z
tags: 
editor: markdown
dateCreated: 2021-05-24T10:36:40.985Z
---

# Calcule de masques

Pour savoir ce qu’est un masque et un adresse ip, voir [cet article](https://wiki-tech.io/Réseau/Protocoles/Bases).

## Qu’est-ce que la méthode magique ?

La méthode magique est une méthode qui va vous permettre de calculer très facilement des plages d’adresses réseau, et bien plus encore !

### Le nombre magique

Pour utiliser la méthode magique, nous allons devoir utiliser **le nombre magique** pour faire des calculs magiques avec nos cerveaux magiques…

**Qu’est-ce que le nombre magique ?**

Le nombre magique est simplement un calcul fait à partir de l’octet significatif du masque.

Il est égal à **256 – octet significatif.**

Par exemple dans notre exemple précédent, le masque était 255.224.0.0  
On voit vite que l’octet significatif (celui où la séparation a lieu) est 224.

Notre nombre magique vaut donc 256 – 224 = 32

### Que faire avec le nombre magique ?

Il va nous permettre de calculer instantanément la première et la dernière adresse de notre plage.

Pour cela, il va falloir écrire tous les multiples du nombre magique (jusqu’à 256 bien sûr)  
Allons-y pour les multiples de 32 !  
0, 32, 64, 96, 128, 160, 192, 224, 256.

Et maintenant, nous allons simplement appliquer les règles suivantes:

La première adresse du réseau sera le multiple du nombre magique, inférieur ou égal à l'octet correspondant dans l'adresse.La dernière adresse du réseau sera le multiple suivant, moins 1.

Notre exemple sera plus parlant:  
Dans notre masque, l’octet significatif est le deuxième (255.224.0.0)  
Nous allons donc prendre le deuxième octet de notre adresse (192.168.0.1), soit 168.

La première adresse du réseau sera donc le multiple du nombre magique, strictement inférieur à 168.  
En regardant la liste des multiples, on trouve très vite 160 !  
0, 32, 64, 96, 128, 160, 192, 224, 256.

La dernière adresse du réseau sera le multiple suivant, moins 1.  
Le multiple suivant est 192. Auquel on enlève 1 pour trouver 191.

La première adresse de la plage est donc 192.160.0.0 et la dernière 192.191.255.255.  
(On a ajouté les 0 pour la première et les 255 pour la dernière car tous les bits qui suivent sont à 0 ou à 1 selon qu’on veut la première ou la dernière)

La méthode magique nous a permis de calculer une plage d’adresses **sans avoir à faire de calculs binaires** !

C’est quand même beau… non ? 

### Amélioration de la méthode magique.

Et bien oui, nous pouvons encore frapper plus fort !

L’idée n’est pas non plus révolutionnaire…  
Il s’agit simplement de ne pas calculer tous les multiples du nombre magique, mais seulement ceux qui sont intéressants.

Prenons un nouvel exemple:  
10.45.185.24/255.255.248.0

Le nombre magique vaut: 256 – 248 = 8  
L’octet significatif du masque est le troisième, ce qui correspond à 185 dans l’adresse.

Nous devons donc trouver le multiple de 8 strictement inférieur à 185…  
Pas la peine de commencer à 0 !  
8 \* 10 = 80, on est en dessous de 185  
8 \* 20 = 160, on est en dessous, mais on se rapproche  
Commençons donc à 160:  
160, 168, 176, 184, 192… STOP ! On est au dessus de 185.

Le multiple strictement inférieur est 184, celui du dessus moins un vaut 191.

Ce qui nous donne pour la première adresse 10.45.184.0, et pour la dernière 10.45.191.255.

Facile non ?

Mais nous pouvons encore frapper plus fort !  
Car trouver la première et la dernière adresse d’une plage est utile, mais découper un plage d’adresses en sous-réseaux l’est souvent encore plus.  
Et la méthode magique va s’avérer redoutable !

---

# Découpage d’une plage d’adresses

En tant qu’administrateur systèmes et réseaux, vous devrez souvent découper une plage d’adresses pour coller au besoin de votre entreprise.

Vous avez à votre disposition une grande plage que vous désirez découper en plusieurs plages plus petites pour séparer les machines et les personnes au sein de l’entreprise.

## Un exemple concret de découpage

Vous avez en charge le réseau d’une petite entité d’une entreprise. L’administrateur général vous laisse à disposition le réseau:  
192.168.160.0/255.255.224.0

Vous avez dans votre entité trois catégories de personnel:

550 techniciens

130 commerciaux

10 directeurs

Il vous faut donc découper la plage d’origine en **trois sous-réseaux** pour chacune de ces populations.

#### Etape 1: Calcul de la plage d’origine

Ceci n’est pas obligatoire, mais nous permettra de connaître l’ensemble de la plage et de savoir si jamais nous dépassons de cette plage par erreur.

Allons-y !  
1- Le nombre magique vaut: 256 – 224 = 32  
2- L’octet significatif de l’adresse vaut 160, qui est un multiple de 32 ! Ce sera donc la première adresse, la dernière étant 160 + 32 – 1 = 191  
3- La première adresse est 192.168.160.0 et la dernière est 192.168.191.255

Maintenant, nous allons devoir calculer les plages pour chacune des populations.

#### Etape 2: Calcul des masques

Mais par quoi commencer ?

La seule information que nous avons est le nombre de personnes de chaque population. Et cela tombe bien, car nous savons que la taille d’une plage dépend de son masque. Donc si on connait le nombre d’adresses nécessaire, nous pouvons en déduire le masque.  
La formule est: **nb adresses = 2nb de 0 dans le masque**

Pour les techniciens, qui sont 550, le réseau devra contenir 1024 adresses (la puissance de 2 supérieure) soit 210  
Le masque contiendra donc 10 zéros, soit:  
11111111.11111111.11111100.00000000  
Soit en décimal:  
255.255.252.0

Nous pouvons faire pareil pour les commerciaux:  
130 < 28  
Le masque est donc:  
255.255.255.0

Et pour les directeurs, nous trouvons:  
10 < 24  
Le masque est donc:  
255.255.255.240

Nous avons les masques pour nos trois populations, il ne nous reste plus qu’à y associer des adresses pour avoir nos plages.

#### Etape 3: Calcul des plages

C’est ici que la méthode magique va nous être utile, car elle permet facilement de trouver les premières et dernière adresse d’une plage.

Nous allons donc commencer par les techniciens.  
Notre plage de départ démarre en 192.168.160.0. Et bien nous allons commencer la plage des techniciens à cette adresse, et allons trouver l’adresse de fin grâce au masque.

Calculons le nombre magique: 256 – 252 = 4  
Le prochain multiple de 4 après 160 est 164 – 1 = 163  
La dernière adresse pour les techniciens est donc 192.168.163.255

Pour les commerciaux, nous allons donc démarrer à l’adresse juste après pour ne pas empiéter sur la plage des techniciens, soit 192.168.164.0.  
Nous allons nous passer du nombre magique pour les commerciaux car la coupure se fait parfaitement entre deux octets sur le masque.  
L’adresse de fin est donc facilement calculée à 192.168.164.255.

Nous démarrons après pour les directeurs, à l’adresse 192.168.165.0.  
Le nombre magique vaut 256 – 240 = 16  
La dernière adresse est donc 192.168.165.15 !

#### Résultat

Nous avons donc défini les trois plages:

Tech: 192.168.160.0/255.255.252.0 soit les adresses allant de 192.168.160.0 à 192.168.163.255

Comm: 192.168.164.0/255.255.255.0 soit les adresses allant de 192.168.164.0 à 192.168.164.255

Dirs: 192.168.165.0/255.255.255.240 soit les adresses allant de 192.168.165.0 à 192.168.165.15

Nous remarquons que pour le réseau des directeurs, l'adresse 192.168.165.15 est une adresse de broadcast...

Tout s’est bien passé, mais…  
Il est très facile de placer les plages d’adresses en partant de la plus grande à la plus petite, mais l’inverse est très très très complexe 

## Quand ça se complique

Imaginons que nous ayons 120 secrétaires qui débarquent sur notre réseau… YOUHOU !!!  
Calmons nous, cela reste un exemple, dans la vrai vie vous ne verrez pas débarquez 120 secrétaires comme cela 

  
Nous voulons leur créer une nouvelle plage, mais sans toucher aux réseaux existants.

Si nous prenons la même méthode que précédemment, nous allons nous planter. 

  
Voyons pourquoi.

Nous avions fini la plage des directeurs à l’adresse 192.168.165.15, nous allons donc démarrer celle des secrétaires à l’adresse suivante, soit 192.168.165.16.  
Le masque pour les secrétaires sera:  
120 < 27  
Soit 255.255.255.128  
Le nombre magique vaut 256 – 128 = 128  
La plage des secrétaires va donc finir au prochain multiple de 128 moins 1, soit 127.

Nous avons donc défini la plage des secrétaires allant de 192.168.165.16 à 192.168.165.127…

Mais cela ne marche pas ! :colere2:

D’abord car il n’y a pas assez d’adresses. De 16 à 127, nous n’avons que 112 adresses, pas assez pour nos 120 secrétaires.

Ensuite, et c’est le plus grave, notre plage n’est pas celle que nous pensons…

En effet, si nous reprenons la méthode magique à 0, cela nous donne.  
Le nombre magique est 128.  
les multiples de 128 sont 0, 128 et 256.  
Notre plage va donc aller de **0 à 127**, et **non de 16 à 127** !

Nous empiétons donc sur les adresses des directeurs !!

Oui, mais comment faire ?

Et bien il suffit de prendre le multiple du nombre magique suivant !  
Nous allons commencer notre plage non pas en 192.168.165.16, mais en 192.168.165.128, et donc finir en 192.168.165.255

Et là nous avons bien défini un réseau d’au moins 120 adresses et qui n’empiète pas sur le réseau des directeurs !

Cependant, nous avons laissé un trou… Les adresses de 16 à 127 ne sont pas utilisées.

Mais c’est **normal**, et ce n’est pas grave de toute façon. Nous pourrons utiliser ces adresses pour des petits réseaux par la suite si nous le souhaitons.

Quand on place un réseau plus grand que le précédent dans une plage, il est **nécessaire** de sauter une certaine plage d’adresses.

Le principe est simple, vu que nous travaillons avec des réseaux dont la taille est un multiple de 2, un petit réseau pourra toujours démarrer sur un multiple d’un grand réseau.

Par exemple, tout multiple de 8 est un multiple de 16:  
0, 16, 32, 48…  
0, 8, 16, 24, 32, 40, 48

On pourra donc toujours placer une petite plage d'adresses derrière une plage précédente plus grande.

Et on pourra seulement parfois placer une grande plage derrière une petite, mais dans ce cas il faudra faire attention et bien utiliser la méthode magique.

---

# Exercices

### Premier exemple

Découpez la plage suivante en trois sous-réseaux:  
10.47.192.0/255.255.240.0

Avec les populations suivantes:

880 techniciens

400 commerciaux

60 directeurs

D’abord, on calcules les masques pour chaque population:  
Techniciens: 880 < 210 ce qui nous donne le masque 255.255.252.0  
Commerciaux: 400 < 29 ce qui nous donne le masque 255.255.254.0  
Directeurs: 60 < 26 ce qui nous donne le masque 255.255.255.192

Ensuite on calcule la plage des techniciens:  
Le nombre magique vaut 256 – 252 = 4  
La première adresse est 10.47.192.0 (donnée par l’énoncé) et la dernière 10.47.195.255

Puis celle des commerciaux:  
Le nombre magique vaut 256 – 254 = 2  
La première adresse est 10.47.196.0 (donnée par la fin de la plage des techniciens) et la dernière 10.47.197.255

Enfin celle des directeurs:  
Le nombre magique vaut 256 – 192 = 64  
La première adresse est 10.47.198.0 (donnée par la fin de la plage des commerciaux) et la dernière 10.47.198.63

### Second exemple… le même que le premier !

En fait l’énoncé est le même, mais l’on vous demande de commencer par les directeurs, puis les commerciaux, et enfin les techniciens.

Ouillouillouille ! 

La bonne nouvelle, c’est que les masques restent les mêmes !  
Techniciens: 255.255.252.0  
Commerciaux: 255.255.254.0  
Directeurs: 255.255.255.192

On passe donc à la plage des directeurs:  
Le nombre magique vaut 64.  
La première adresse est 10.47.192.0 et donc la dernière va être 10.47.192.63

Nous serions tentés de continuer pour la plage des commerciaux à l’adresse suivante, mais l’on sait que l’on prendrait alors des risques…

Pour les commerciaux, le nombre magique vaut 2. Il faut donc que la première adresse démarre sur un nombre pair sur son troisième octet (l’octet significatif dans le masque)  
On ne peut pas démarrer en 192 puisque quelques adresses sont déjà prises par les directeurs. Il faut donc démarrer en 194.  
Ce qui nous donne 10.47.194.0 pour la première adresse et 10.47.195.255 pour la dernière adresse.

On continue pour les techniciens.  
Le nombre magique vaut 4.  
192 est un multiple de 4, mais il est déjà utilisé par les directeurs. On peut par contre prendre 196.  
Ce qui nous donne 10.47.196.0 pour la première adresse et 10.47.199.255 pour la dernière adresse.

On récapitule:  
Directeurs: De 10.47.192.0 à 10.47.192.63  
Commerciaux: De 10.47.194.0 à 10.47.195.255  
Techniciens: De 10.47.196.0 à 10.47.199.255

Et ça marche !

Heu… oui mais là on finit plus loin que la première fois, on n’aurait pas gâché plus d’adresses ?

Non, nous en avons gâché exactement le même nombre. Sauf qu’ici on le voit bien car les adresses gâchées sont dans les “trous” que nous avons laissés entre chaque plage, alors que dans le premier cas il y a des adresses gâchées, mais elles se situent après nos trois plage.  
**Le résultat est exactement le même !**