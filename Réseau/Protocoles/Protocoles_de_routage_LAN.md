---
title: Les protocoles de routage LAN
description: Tout comprendre sur le routage LAN.
published: true
date: 2021-06-27T14:27:48.918Z
tags: routage, lan, vecteur de distance, état de liens, rip, ospf, igrp, is-is
editor: markdown
dateCreated: 2021-06-25T14:49:58.493Z
---

# Introduction

L’objectif d’un [routeur](https://wiki-tech.io/R%C3%A9seau/Equipements/Routeur) est de toujours disposer de la route optimale pour aller d’un endroit à l’autre. Pour cela il y a deux solutions :

-   **Route statique** : un routeur avec des routes statiques utilise une route avec son protocole telle qu’elle a été configurée par l’administrateur réseau.
-   **Route dynamique** : un routeur configuré avec des routes dynamiques utilise un protocole de routage réseau qui ajuste automatiquement les routes à emprunter en fonction des modifications de topologie ou de trafic.

# Caractéristiques

Pour rappel, le routage consiste en la signalisation des directions pour aiguiller les datagrammes de réseau en réseau. Tous les protocole de routage ont un seul objectif : chercher à minimiser une métrique. Cette métrique représente le « coût » pour aller d’un endroit à un autre du réseau.

Vous trouverez ci-dessous la liste des composants d’une métrique :

-   **Bande passante** (en fonction du type de liaison) : Eth 10Mb/s, série 64kb/s, etc…
-   **Délai** (en fonction de la bande passante des liaisons intermédiaires, de la taille du stockage temporaire du routeur, de la congestion du réseau, de la distance physique)
-   **Charge** (en fonction de la quantité de trafic d’une ressource réseau)
-   **Fiabilité** (en fonction du taux d’erreur)
-   **Nombre de sauts** (en fonction du nombre de routeurs jusqu’à la destination)
-   **Coût** (en fonction de la bande passante, de la dépense monétaire ou d’une autre mesure attribuée par un administrateur réseau)

Les protocoles de routage doivent converger le plus rapidement possible, ce qui explique leurs propriétés :

-   **Algorithme simple** : gain en mémoire et en temps processeur
-   **Optimisation** : prise en compte de plusieurs métriques et pondération entre elles
-   **Efficacité et stabilité**
-   **Adaptation rapide à des évènements du réseau** (modification topologique, panne, …)

Dans les LAN, les protocoles de routages sont regroupés en deux familles : vecteurs de distance (RIP et IGRP) et à états de liens (OSPF). Nous allons étudier ces deux familles dans la suite de cet article.

# La différence entre forwarding et routing

Le forwarding consiste à déterminer le prochain saut pour un datagramme vers la destination indiquée dans son adresse de destination et à commuter le datagramme d’une interface d’entrée vers une interface de sortie.

Le routing consiste à échanger le contenu de sa table de routage avec ses voisins pour déterminer les chemins dans une architecture multi-réseaux. Pour ce faire, un protocole de routage est utilisé.

> Sous Linux, le forwarding est réalisé par le noyau alors que le routing est réalisé par un programme (daemon).
{.is-info}


# Le routage par vecteur de distance

Ce type de protocole détermine la direction (le prochain saut) et la distance (le nombre de sauts) vers n’importe quel réseau de l’inter-réseau. Il envoie périodiquement tout ou partie de sa table de routage aux routeurs voisins. À la réception d’une mise à jour de routage, une vérification de tous les chemins connus est faite et des modifications sont apportées si nécessaire.

![](/images/routage_vecteur_distance.png)



## RIP v1 (RFC 1058)

Le protocole RIP v1 (Routing Information Protocol) a été conçu en 1988. 

Il utilise le protocole UDP en broadcast (`255.255.255.255`) pour échanger les informations de routage. La gestion des routes se fait via l’utilisation de timer :

-   **Update timer** : intervalle de transmission des paquets de mise à jour (30 secondes)
-   **Invalid timer** : temps de non mise à jour d’une entrée pour une route avant qu’elle ne soit marquée comme invalide dans la table de routage (180 secondes)
-   **Holddown timer** : temps après lequel l’entrée non mise à jour n’est plus examiné durant le processus de recherche du meilleur chemin (180 secondes)
-   **Flush timer** : temps après lequel l’entrée non mise à jour est supprimée de la table de routage (240 secondes)

Voici les caractéristiques principales de RIP v1 :

-   **Métrique** : nombre de sauts
-   **Algorithme** : Bellman-Ford
-   Il n’y a **pas de prise en compte du débit des liaisons**
-   **Maximum 15 sauts**
-   Type **classful** ce qui veut dire qu’il n’y a pas d’information de masque réseau associé aux destinations. En d’autres termes, il ne prend pas en charge les masques de sous-réseau de longueur variable.

## RIP v2 (RFC 2453)

Des faiblesses de RIP v1 (coût de la diffusion par broadcast, pas adapté aux masques à longueur variable) est né RIP v2 en 1993. Contrairement à la première version, RIP v2 utilise le multicast (`224.0.0.9`) pour envoyer les informations de routage.

Voici les caractéristiques principales de RIP v2 :

-   **Métrique** : nombre de sauts
-   **Algorithme** : Bellman-Ford
-   Il n’y a **pas de prise en compte du débit des liaisons**
-   **Maximum 15 sauts**
-   Type **classless** ce qui veut dire que le masque est associé aux destinations.

## IGRP

Le protocole IGRP (Interior Gateway Routing Protocol) a été développé par Cisco en partie pour remédier aux limitations de RIP. Les mises à jour sont transmises immédiatement à chaque changement. Contrairement à RIP qui transmet la table de routage complète, IGRP n’envoie que la ligne qui subit une modification.

Voici les caractéristiques principales d’IGRP :

-   **Métrique** : nombre de sauts, délai, bande passante, charge et fiabilité
-   **Algorithme** : Dijkstra
-   **Maximum 255 sauts**
-   Type **classful** ce qui veut dire qu’il n’y a pas d’information de masque réseau associé aux destinations. En d’autres termes, il ne prend pas en charge les masques de sous-réseau de longueur variable.

## Optimisation

Deux fonctions permettent d’empêcher ou de réduire pour certains cas les boucles de routage et d’optimiser le reroutage des flux en cas de tombé d’un lien. Les voici :

-   **Split-Horizon** : ne pas réannoncer à un routeur une route qu'on a apprise par lui. En effet, même si la route ainsi renvoyée ne peut être immédiatement celle de plus court chemin (+2 à la métrique à cause de l'aller-retour sur le lien), il se peut qu'en cas de rupture d'un lien amont sur le routeur d'origine, celui-ci puisse croire que la route optimale passe par le routeur qui lui a réannoncé sa propre route.
-   **Poison-Reverse** : perfectionnement de Split-Horizon en réannonçant la route apprise par un routeur à celui-ci, mais en lui affectant une métrique infinie (= 16) afin de signifier à celui-ci que le chemin n'est pas à utiliser. Cette méthode permet de casser immédiatement les boucles de routage alors que le simple split-horizon implique d'attendre la prochaine mise à jour des tables de routage.

# Le routage par état de liens

![](/images/routage_etat_liens.png)



Les protocoles de routage par état de liens ont été conçu principalement pour pallier les limitations des protocoles de routage à vecteur de distance (notamment en terme de taille). Ils gèrent une base de données d'informations topologiques sur laquelle ils appliquent un algorithme de calcul du plus court chemin d'abord (Shortest Path First (SPF) ou algorithme de Dijkstra).

Le fonctionnement du routage par état de liens est le suivant : 1️⃣ Echange de paquets LSA (Link State Advertisement) qui permettent 2️⃣ de construire la base de données topologique sur laquelle 3️⃣ nous appliquons l’algorithme SPF qui permet de 4️⃣ construire l’arbre de plus court chemin dont la racine est le routeur. De cet arbre 5️⃣, la table de routage est tirée.

L’envoie des mises à jour est réalisé à des intervalles moins fréquents que pour le routage  par vecteur de distance (toutes les 30 minutes par exemple). Le plus important est d'avoir une base de données topologique à jour et identique sur tous les routeurs.

Les principaux protocoles à états de liens sont OSPF et IS-IS.