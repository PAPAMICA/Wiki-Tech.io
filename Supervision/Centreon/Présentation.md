---
title: Centreon - Présentation
description: Qu’est ce qu’est Centreon ?
published: true
date: 2022-04-01T21:25:39.393Z
tags: 
editor: markdown
dateCreated: 2021-05-24T10:37:18.321Z
---

![Centreon | Supervision Informatique : Infrastuctures Hybrides, Cloud, AIOps](https://static.centreon.com/wp-content/uploads/2019/10/Centreon_Logo_RVB_HD_Cut.png?x55598)

**Auteur : Antoine JOVELIN**  
**Admin Système Réseau - 49100 Angers**

# Présentation

Centreon est un outil de supervision et de monitoring possédant de nombreuses fonctionnalités.  
C'est un programme modulaire qui se décompose en plusieurs parties :

-   L'ordonnanceur qui se charge d'ordonnancer les tâches de supervision
-   Une interface Web
-   Les plugins

Il permet de connaître à l’aide de son interface web et en temps réel l’état des périphériques supervisés, comme par exemple :

-   Utilisation de la RAM
-   Utilisation du processeur
-   L’espace disque
-   La communication sur le réseau
-   Surveillance des services
-   Notification par email
-   Journalisation des évènements
-   Etc.

La liste des possibilités est longue.  
Centreon fonctionne sur un principe de « check » à l’aide du protocole SNMP ou encore de « check » à l’aide d’un agent installé sur l'hôte ciblé. L’agent Windows est « NSClient » et l’agent Linux est « Xinetd ».  
Il est également possible de faire des checks par « rebond » via le protocole SSH.

![](/images/centreon-1.png)

#  Architectures

## Architecture simple

Une architecture est appelée simple lorsqu’on associe tous les modules de Centreon dans un unique serveur.  
A noter toutefois que ce type d’architecture comporte de nombreux inconvénients : 

-   En cas de perte du serveur, on perd toute vues de supervision possibles car tous les modules sont dans le même serveur.
-   Les agents envoyant les informations vers un point unique du réseau, la bande passante risque de saturer très rapidement en fonction du nombre d'hôtes supervisés dans l'infrastructure.

Exemple d’un schéma d’une architecture dite simple 

![](/images/centreon-2.png)

## Architecture distribuée

Une architecture distribuée se compose en général d’un serveur de supervision et d’un ou plusieurs « poller ».   
Elle permet notamment : 

-   De répartir la charge sur plusieurs serveurs de supervision
-   De limiter l’impact du flux de supervision sur les interconnexions de réseaux
-   L’isolation de réseau.

Une relation de master-slave peut être mis en place sur la base de données mais aussi sur le Central.  
Exemple d’un schéma d’une architecture dite distribuée : 

![](/images/centreon-3.png)

# Prérequis

-   Des connaissances en système d’exploitation GNU/Linux
-   Des connaissances en système d’exploitation Windows
-   Des connaissances en réseau, sur les protocoles réseau et leurs numéros de ports.
-   Des connaissances sur les services Windows et Linux.
-   Du matériel réseau compatible avec le protocole SNMP
-   Des connaissances sur le protocole SNMP
-   Un ou plusieurs serveurs remplissant les prérequis matériels disponibles sur le site de Centreon
-   Un serveur mail fonctionnel pour recevoir des notifications
-   Des connaissances sur le fonctionnement des MIP
-   Une connexion Internet fonctionnelle pour les checks distant