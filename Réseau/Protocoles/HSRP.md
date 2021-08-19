---
title: Réseau - HSRP
description: Qu'est-ce que c'est et à quoi ça sert ?
published: true
date: 2021-06-25T22:00:07.037Z
tags: 
editor: markdown
dateCreated: 2021-05-24T10:36:38.820Z
---

![Core Cisco](https://formip.com/wp-content/uploads/2018/01/hsrp-fhrp-redondance-de-passerelle-par-defaut-3.png)

# Présentation

Le terme HSRP est un protocole qui permet de mettre en place la tolérance de panne d’une passerelle pour les équipements qui se connectent à notre réseau.

HSRP est l’abréviation de **H**ot **S**tandby **R**outer **P**rotocol.

Le HSRP est un protocole propriétaire de Cisco.

# Fonctionnement

Dans chaque réseau où le HSRP est actif, on définit un groupe de routeurs. Chaque groupe est associé à une adresse IP virtuelle distincte.

Dans un groupe, un routeur actif est défini : c’est celui qui aura la priorité la plus élevée. Les autres routeurs sont en standby et écoutent les requêtes émises par le routeur actif. Périodiquement, les routeurs du groupe échangent des messages pour s'assurer que les routeurs du groupe sont encore joignables. Par défaut, les messages sont envoyés toutes les 3 secondes, et un délai de plus de 10 secondes sans message de la part du routeur actif entraîne la promotion du routeur Standby en actif.

Si le routeur actif principal devient inaccessible, un autre routeur sera élu : celui qui a la deuxième priorité la plus élevée. Tous les messages entre les routeurs sont échangés en utilisant l'adresse multicast 224.0.0.2 via UDP sur le port 1985.

Si un routeur A découvre qu'un autre routeur B est actif pour le même groupe et dans le même réseau et que ce dernier a une priorité inférieure à la sienne, alors le routeur A reprend le rôle de routeur actif.

Le changement de routeur actif est transparent pour les hôtes qui utilisent l'adresse virtuelle.

# Limitations du HSRP

Il n'y a pas de partage de charge entre les routeurs.

L'utilisation de l'adresse multicast 224.0.0.2, permet à tous les routeurs du réseau de recevoir les messages.

# Authentification

Le protocole HSRP (Hot Standby Routing Protocol) est un protocole propriétaire Cisco celui-ci permet d’obtenir une continuité de service de nos routeurs.

###### Définition du mot de passe : 

Il est conseillé de personnaliser un mot de passe pour les échanges et de ne pas utiliser celui par défaut. Ainsi, cela diminue le nombre de chances qu’un pirate casse l’HSRP. Pour changer le mot de passe, il faut utiliser la commande suivante :

**standby 10 authentication wikitech**

###### Hashage MD5 :

Cisco permet l'utilisation de l’algorithme MD5 afin de hasher les échanges. Dans ce cas, le mot de passe n’est plus tramis dans le champ « Authentification data » celui-ci devient égale à 0. Pour cela, il faut rajouter la commande suivante dans tous les routeurs membre du groupe HSRP :

**standby 10 authentication md5 hsrp**

# **Exemple de configuration** 

Il nous faut à minima deux routeurs de marque Cisco (A et B) utilisant le protocole HSRP pour produire une tolérance de panne. Le routeur A utilisera l'adresse IP 192.168.1.252 avec un masque de sous-réseau de 255.255.255.0, tandis que le routeur B utilisera l'adresse IP 192.168.1.253 avec le même masque de sous-réseau.

L'adresse IP virtuelle est 192.168.1.254. Cela correspond à l’IP que l’on devra renseigner comme passerelle par défaut sur nos équipements de notre réseau.

**Routeur A**

*interface fastethernet 0/0*

*ip address 192.168.1.252 255.255.255.0*

*standby 10 ip 192.168.1.254*

*standby 10 priority 105*

*standby 10 preempt*

*standby 10 authentication wikitech*

*standby 10 authentication md5 hsrp*

**Routeur B**

*interface fastethernet 0/0*

*ip address 192.168.1.253 255.255.255.0*

*standby 10 ip 192.168.1.254*

*standby 10 priority 100*

*standby 10 preempt*

*standby 10 authentication wikitech*

*standby 10 authentication md5 hsrp*