---
title: 🔐 WireGuard
description: Comprendre et installer ce VPN nouvelle génération
published: false
date: 2021-06-28T20:43:52.648Z
tags: linux, réseau, routage, vpn, wireguard
editor: markdown
dateCreated: 2021-06-27T21:09:12.144Z
---

![Logo WireGuard](https://www.wireguard.com/img/wireguard.svg)

# Présentation

WireGuard est un protocole VPN de nouvelle génération sous licence GPLv2 (ou MIT, BSD, Apache 2.0 ou GPL suivant le contexte) créé par Jason A. Donenfeld. Le site officiel étant accessible via ce lien : https://www.wireguard.com/.

WireGuard se veut être plus **simple**, **rapide** et **sécurisé** que les protocoles VPN communs que sont OpenVPN et IPsec. 
- La **simplicité** de configuration se passe en une seule phase standardisée contrairement à IPsec fonctionnant avec deux phases souvent difficiles à appréhender pour des novices. De plus la non utilisation de certificats, comme on la retrouve avec OpenVPN, simplifie la gestion à plus long terme et ne nécessite pas la création d'une PKI.
- La **sécurité** est assurée avec l'utilisation de primitives cryptographiques modernes mais aussi par la mise à disposition d'une seule combinaison de méthode de chiffrement. De plus le code complet de WireGuard tient sur environ 4 000 lignes ce qui facilite les audits de sécurité et réduit sa surface d'attaque.
- La **rapidité** tient en grande partie des points précédents mais aussi parce que WireGuard fonctionne au niveau du noyau (seulement sous Linux). Les tests présents sur le site officiel (https://www.wireguard.com/performance/) démontrent un très fort avantage de WireGuard sur OpenVPN et un avantage un peu plus ténu sur IPsec, à la fois en débit maximal atteint et en temps de réponse. A savoir que d'autres tests sont disponibles sur Internet et tendent à placer WireGuard comme le plus performant même si l'écart n'est pas toujours aussi important.

> C'est un **VPN de niveau 3** du modèle OSI. C'est-à-dire que c'est la couche *Réseau* (IP, ICMP, ARP et DHCP principalement) qui est redirigée dans le tunnel VPN. Le client VPN pourra atteindre le réseau distant mais ne sera pas vu comme appartenant directement à ce réseau comme on l'aurait avec un VPN de niveau 2 (par exemple avec OpenVPN et l'utilisation de sa carte TAP).
{.is-info}


# Comment fonctionne WireGuard ?

## Quels sont les prérequis ?

WireGuard est intégré au noyau Linux à partir de la **version 5.6 du noyau Linux**. Toutes distribitions ayant une version égale ou supérieur est donc compatible pour l'utilisation de WireGuard. 
Une recherche sur Internet pour vérifier que sa distribution favorite est compatible avec WireGuard est tout de même conseillée.

Pour les autres environnements il est possible d'aller vérifier sur le site WireGuard qu'un portage existe : https://www.wireguard.com/install/. A titre informatif les autres OS principaux sont compatibles à partir de :
- Windows 7 / Windows Server 2012 
- macOS 10.14 Mojave
- Android 5.0
- iOS 12.0 / iPadOS 12.0

> Ces informations de compatibilités sont celles annoncées pour la date du 28/06/2021.
{.is-warning}


## Quels flux sont utilisés ?

WireGuard fonctionne uniquement avec le **protocole UDP** (ce qui est recommandé pour un VPN afin d'éviter les problèmes que provoque l'encapsulation TCP dans du TCP). 

Aucun port standard n'a été affecté à WireGuard par l'IANA ([liste des ports assignés](https://www.iana.org/assignments/service-names-port-numbers/service-names-port-numbers.txt)) mais les documentations utilisent généralement le port 51820.


## Qu'est-ce que le routage cryptographique ?

Le nouveau concept central autour du protocole WireGuard est celui du routage cryptographique. Le routage cryptographique associe des adresses IP ou des sous-réseaux à des clefs publiques.
Les clefs cryptographiques fonctionnent à peu près comme celles que l'on retrouve avec le protocole SSH : une paire de clef, l'une dite privé et l'autre publique, sont utilisées afin d'utiliser des mécanismes de chiffrement asymétrique.

Admettons la configuration suivante :

|         |  IP source  |  IP WireGuard | Clef privée | Clef publique |
|--------:|:-----------:|:-------------:|:-----------:|:-------------:|
| Serveur |  1.2.3.4/32 | 10.0.0.254/24 |     ABC     |      XYZ      |
|  Client |  6.7.8.9/32 |  10.0.0.1/24  |     123     |      789      |

Du côté du serveur une association entre l'IP WireGuard du client, *10.0.0.1/24*, sera faite avec sa clef publique, *789*. Le traitement des paquets du côté du serveur seront traités comme ceci :
- Tous paquets entrants seront déchiffrés, à l'aide de la clef privée du serveur *ABC*, et si l'utilisation de la clef publique *789* ainsi que l'IP WireGuard correspond à *10.0.0.1/24* alors les paquets seront acceptés et seront routés normalement.
- Tous paquets sortants ayant pour destination *10.0.0.1/24* seront chiffrées à l'aide de la clef publique associée à l'IP, ici *789*, et envoyée vers la destination via son IP source, ici *6.7.8.9/32*.

> A noter que l'exemple est très simpliste, en condition réelle une clef privée peut être associée à différentes IP ou réseaux pour l'interface WireGuard. De même plusieurs clients sont en général rattachés à un serveur, la non duplication des routes ou adresses est essentielle pour ne pas causer des problèmes de routage ainsi que des difficultés pour retrouver la clef publique du client. 
{.is-info}


# Installation et configuration 

## Serveur

### Configuration simplifiée avec wg-quick

### Configuration manuelle

### Informations sur les clients connectés

### Générer le QR code de configuration

## Client

### Configuration du client sous Linux

#### Utilisation de wg-quick

#### Configuration manuelle

#### Configuration avancée : utilisation des espaces de noms réseaux

### Configuration du client sur les autres plateformes



---
*Rédacteur Lucas MEYER*