---
title: Spanning-Tree
description: Qu’est-ce que c’est et à quoi ça sert ?
published: true
date: 2021-06-14T08:02:20.955Z
tags: 
editor: markdown
dateCreated: 2021-05-24T10:36:45.380Z
---

![Spanning Tree Consulting](https://www.spanningtreeconsulting.com/images/spanning-tree-logo.png)

# Qu’est-ce que c’est ?

Le **Spanning Tree Protocol** (algorithme de l’arbre recouvrant, aussi appelé **STP**) est un protocole réseau de **niveau 2** permettant de déterminer une topologie réseau sans boucle (appelée arbre) dans les LAN avec ponts. Il est défini dans la norme IEEE 802.1D et est basé sur un algorithme décrit par Radia Perlman en 1985.

# A quoi sert-il ?

L’objectif des réseaux est de faire en sorte que les paquets arrivent à destination. Une solution est de dupliquer les équipements physique pour qu’en cas de panne sur l’un d’eux, l’autre équipement prenne le relai; **on appelle ça la redondance ou la résilience**.

## Architecture non redondée

![](https://i0.wp.com/reussirsonccna.fr/wp-content/uploads/2012/06/STP.png?resize=460%2C86)

Sur le schéma ci-dessus, on voit bien que si le switch tombe (panne électrique, bug…), plus aucune communication entre les ordinateurs A et B n’est possible.

## Architecture redondée

Maintenant que l’on souhaite que les paquets entre les ordinateurs A et B transitent même en cas de panne matériel, créons cette nouvelle architecture:

![](https://i0.wp.com/reussirsonccna.fr/wp-content/uploads/2012/06/STP2.png?resize=460%2C171)

Avec cette architecture, on voit bien que si le switch du haut ne fonctionne plus, le switch du bas peut tout même transmettre les paquets de A vers B et de B vers A.

## Les 3 problèmes récurrents d’une connexion redondante

### _1er problème: Tempète de broadcast_

Sur l’architecture redondée précédente, imaginons que la station A envoi un message de broadcast (trame niveau 2 avec comme adresse MAC de destination FFFF.FFFF.FFFF). *Que se passe-t-il?*

![](https://i0.wp.com/reussirsonccna.fr/wp-content/uploads/2012/06/STP_broadcast_storm.png?resize=460%2C171)

-   Le switch du haut reçoit la trame sur son port, **extrait l’adresse MAC** de destination (FFFF.FFFF.FFFF) et **la duplique sur tous ses ports** car c’est une adresse de broadcast. La trame sort donc du switch du haut et se dirige vers le switch du bas
-   idem pour le switch du bas; il reçoit la trame sur son port, **extrait l’adresse MAC** de destination (FFFF.FFFF.FFFF) et **la duplique sur tous ses ports** car c’est une adresse de broadcast. La trame sort donc du switch du bas et se dirige vers le switch du haut
-   et ces trames **tournent sans arrêt** entre les 2 switchs, faisant monter leur CPU à 100% et les font plus ou moins planter (souvent un reboot est nécessaire)

Ce phénomène s’appelle la **tempête de broadcast**, ou **broadcast storm** en anglais.

### _2ème problème: Duplication de trame_

Maintenant, imaginons que la station A envoi une trame vers la station B, donc la trame sera forgée avec les informations suivantes:

-   adresse MAC source: A
-   adresse MAC destination: B

![](https://i1.wp.com/reussirsonccna.fr/wp-content/uploads/2012/06/STP_duplication_trame.png?resize=460%2C171)

*Que se passe-t-il?*

-   Le switch du haut reçoit la trame sur son port (flèche rouge), **extrait l’adresse MAC** de destination (B) et la commute sur le port de droite. La station B reçoit bien la trame de la station A
-   Mais le switch du bas reçoit aussi la trame sur son port (flèche orange), **extrait l’adresse MAC** de destination (B) et la commute sur le port de droite. La station B reçoit donc pour une deuxième fois la trame de la station A

Ce phénomène s’appelle la **duplication de trame**  (pas top comme optimisation réseau 🙂)

### _3ème problème: Instabilité de la table CAM_

Maintenant, regardons un peu ce qu’il se passe côté table CAM – Content Addressable Memory – du switch.

Pour ceux qui ont oublié cette notion, je vous renvoi vers cet article.

![](https://i2.wp.com/reussirsonccna.fr/wp-content/uploads/2012/06/STP_instabilite_CAM.png?resize=481%2C200)

Reprenons  la trame précédente (message de A vers B):

-   la trame arrive sur le port 1 du switch du haut. Le switch **extrait l’adresse MAC source** et **l’insère dans sa table CAM** \[port 1 = adresse MAC A\]
-   la trame arrive aussi sur le port 3 du switch du bas. Le switch **extrait l’adresse MAC source** et **l’insère dans sa table CAM** \[port 3 = adresse MAC A\]

Maintenant que chaque switch a extrait l’adresse MAC source pour l’insérer dans sa table, chacun **extrait l’adresse MAC de destination** (B) et **la compare à sa table**. Comme aucune entrée n’est trouvée, chaque switch va dupliquer la trame sur tous ses ports:

-   le switch du haut envoi la trame sur son port 2
-   le switch du bas envoi la trame sur son port 4

Et c’est là où ça devient cocasse car chaque switch recoit la trame de l’autre switch…

-   le switch du haut reçoit sur son port 2 la trame du switch du bas
    -   le switch **extrait l’adresse MAC source** et **l’insère dans sa table CAM** \[port 2 = adresse MAC A\]. Pour cela, **il supprime l’entrée précédente** qui était \[port 1 = adresse MAC A\]
-   le switch du bas reçoit sur son port 4 la trame du switch du haut
    -   le switch **extrait l’adresse MAC source** et **l’insère dans sa table CAM** \[port 4 = adresse MAC A\]. Pour cela, **il supprime l’entrée précédente** qui était \[port 3 = adresse MAC A\]

On voit ici que les switchs mettent à jour leur table CAM à chaque fois qu’ils reçoivent une trame.

Ce phénomène s’appelle l’**instabilité de la table CAM**.

## Résolution des 3 problèmes

Pour éviter ces 3 problèmes (**tempête de broadcast**, **duplication de trame** et**instabilité de la table CAM**), le protocole spanning-tree a été créé. Comme ces problèmes proviennent du fait que le réseau commuté est face à une boucle physique, le spanning-tree permet d’identifier cette boucle et de la bloquer “logiciellement”.

![](https://i0.wp.com/reussirsonccna.fr/wp-content/uploads/2012/06/STP_block1.png?resize=460%2C171)

Dans notre exemple, tout le trafic passera par le switch du haut pour joindre la station B, le chemin du bas étant bloqué au niveau du port du switch du bas.

Si le switch du haut tombe en panne, le protocole spanning-tree va le détecter et va débloquer le port du bas. A ce moment, tout le trafic passera pour le switch du bas.

Voilà à quoi sert le spanning-tree !

# A retenir

-   En mettant en place une architecture redondée, on est face à 3 problèmes majeurs:
    1.  tempête de broadcast
    2.  duplication de trame
    3.  instabilité de la table CAM
-   Pour résoudre ces problèmes, le spanning-tree a été créé et permet d’éviter les boucles physiques en désactivant un port logiciellement, et le réa-active au besoin pour assurer la résilience du réseau