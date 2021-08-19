---
title: Routeur
description: Qu'est-ce que c'est et comment fonctionne-t-il ?
published: true
date: 2021-06-27T14:29:14.888Z
tags: réseau, routeur, équipements
editor: markdown
dateCreated: 2021-06-25T09:09:49.041Z
---

# Son rôle

Les routeurs sont des équipements d'interconnexion qui fonctionnent au niveau de la couche 3 (couche réseau du [modèle OSI](https://wiki-tech.io/R%C3%A9seau/Protocoles/OSI)). Ils interconnectent des différents sous réseaux d’une entreprise ou le réseau de l’entreprise et le reste du monde via Internet notamment.

Leur rôle consiste à acheminer les paquets de données entre les réseaux, en fonction des informations de la couche 3, en empruntant le chemin le plus court possible. Au-delà des fonctions traditionnelles de routage, d’autres fonctions sont aujourd’hui implémentées dans les routeurs : le filtrage, la translation d’adresse, le relais de messagerie, etc… 

Le besoin de consolidation des équipements réseau a conduit au développement de [commutateurs](https://wiki-tech.io/R%C3%A9seau/Equipements/Switch) de niveau 3 qui intègrent donc les fonctions de routage. L’usage de ce type d’équipements permet de réduire la complexité et le coût du réseau au détriment parfois de la richesse des fonctions disponibles et des performances.

![](/images/routeur.png)



# Le fonctionnement du routage

Le routeur, qui est l'élément d'interconnexion, utilise les informations des datagrammes transportés par les trames pour déterminer le chemin des données. Ce chemin déterminé au niveau 3 est ensuite mise en œuvre au niveau 2 via le protocole ARP. Le routage IP se fait ainsi saut par saut. 

De ce fait, il est possible de présenter le principe de base du routage en deux étapes :

-   **Détermination du chemin** : la couche réseau utilise une table de routage pour déterminer quel est le meilleur chemin à emprunter pour atteindre le réseau de destination. La métrique qui permet de mesurer la qualité d’un chemin est calculée en fonction de la configuration du routeur et du protocole de routage utilisé.
-   **Commutation** : La fonction de commutation permet à un routeur d'accepter un paquet d'une interface et de le transmettre par le biais d'une autre interface. Le paquet pris en charge à une interface est retransmis via une autre interface via le chemin calculé à l’étage précédente.

# Critères de choix d'un routeur

Maintenant que nous comprenons ce qu’est un routeur et comment il fonctionne globalement, il faut savoir comment bien choisir son équipement. Ce choix dépendra de l’architecture réseau visée et des évolutions envisagées.

Les critères ci-dessous permettent de se faire une première idée (la liste n’est pas exhaustive) :

-   Nombre, vitesse et type d’interfaces
-   Capacité de routage (nombre de paquet routés par seconde)
-   Evolutivité
-   Fonctions / services intégrés