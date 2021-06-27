---
title: 🔐 WireGuard
description: Comprendre et installer ce VPN nouvelle génération
published: false
date: 2021-06-27T21:11:04.282Z
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

C'est un **VPN de niveau 3** du modèle OSI. C'est-à-dire que c'est la couche *Réseau* (IP, ICMP, ARP et DHCP principalement) qui est redirigée dans le tunnel VPN. Le client VPN pourra atteindre le réseau distant mais ne sera pas vu comme appartenant directement à ce réseau comme on l'aurait avec un VPN de niveau 2 (par exemple avec OpenVPN et l'utilisation de sa carte TAP).

# Comment fonctionne WireGuard ?

## Quels sont les prérequis ?

## Quels flux sont utilisés ?

## Qu'est-ce que le routing cryptographique ?

# Installation et configuration 

## Serveur

### Configuration simplifiée avec wg-quick

### Configuration manuelle

### Générer le QR Code de configuration

## Client

### Configuration du client sous Linux

#### Utilisation de wg-quick

#### Configuration avancée : utilisation des espaces de noms réseaux

### Configuration du client sur les autres plateformes



---
*Rédacteur Lucas MEYER*