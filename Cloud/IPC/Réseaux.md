---
title: Infomaniak Public Cloud - Les réseaux
description: Créer et gérer les réseaux
published: true
date: 2021-11-09T14:54:53.627Z
tags: openstack, réseau, infomaniak, public-cloud, cloud, ipc
editor: markdown
dateCreated: 2021-11-09T13:48:54.561Z
---

![plan_de_travail_1@4x.png](/images/cloud/infomaniak-public-cloud/plan_de_travail_1@4x.png =600x)

# Introduction
Le Cloud Public d'Infomaniak met à disposision 3 types de réseaux :
- **Réseau externe** (***ext-net1*** & ***ext-v6only1***) : Permet d’avoir une instance directement connectée à internet
- **Réseau floating** (***ext-floating1***) : Permet de réserver une adresse IP publique et de l’attribuer dynamiquement à des ressources
- **Réseau provider** (***ext-provider1***) : Permet de réserver un sous-réseau (/28 ou /29) dédié d’adresses IP publiques

> 	**Réseau provider**
> Attention : **3 adresses IP doivent être dédiées** au fonctionnement du réseau et ne sont donc pas utilisables librement : une pour la **passerelle**, une pour le **DHCP** et une pour le **broadcast**.
{.is-warning}




# Horizon
## Réseau externe
### Connexion d'une instance à ce réseau

## Réseau floating
### Réservation de l'adresse IP
### Configuration de l'adresse IP sur une instance
### Configuration de l'adresse IP sur un routeur

## Réseau provider
### Création du réseau
### Assignation d'un pool IP
### Création du routeur
### Connexion d'une instance à ce réseau

# CLI
## Documentation OpenStack
  - [🔗 OpenStack Docs : network *Documentation officielle*](https://docs.openstack.org/python-openstackclient/xena/cli/command-objects/network.html)
{.links-list}

## Réseau externe
### Création du réseau
### Connexion d'une instance à ce réseau

## Réseau floating
### Création du réseau
### Connexion d'une instance à ce réseau

## Réseau provider
### Création du réseau
### Assignation d'un pool IP
### Création du routeur
### Connexion d'une instance à ce réseau


## Gestion des réseaux
### Afficher un réseau
### Supprimer un réseau