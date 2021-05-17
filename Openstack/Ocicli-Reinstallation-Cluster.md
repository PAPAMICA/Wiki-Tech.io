---
title: Ocicli - Réinstaller un cluster OpenStack
description: Toutes les étapes de réinstallation d'un cluster OpenStack
published: true
date: 2021-05-17T07:38:17.392Z
tags: 
editor: markdown
dateCreated: 2021-05-17T06:58:58.937Z
---

# Préparation

## Mise à jour du serveur
```bash
apt update
apt dist-upgrade
```
> Verifier `openstack-cluster-installer.conf` pour ses mises à jours
{.is-warning}

## Vérifier les clés SSH
Mettez à jours le fichier `/etc/openstack-cluster-installer/authorized_keys` avec les clés SSH des utilisateurs de votre cluster.

## Création de l'image Live Debian
```bash
cd live-image
openstack-cluster-installer-build-live-image
```
> Le processus peut mettre quelques minutes en fonction de votre connection
{.is-info}

# Installation

## Démarrage des serveurs sur l'image Live
```bash
ocicli cluster-reset <CLUSTER-NAME>
ocicli machine-list
```
Vous devriez voir l'ensemble de vos serveur avec le status `live` ou `bootinglive`. Attendez que tous les serveurs aient le statut `live`.

Mettre la variable `initial-cluster-setup` à `yes` :
```bash
ocicli cluster-set <CLUSTER-NAME> --initial-cluster-setup yes 
```
> Voir [Ocicli : Variable "initial-cluster-setup"](/Openstack/Ocicli#variable-de-configuration-initiale-du-cluster)
{.is-info}
