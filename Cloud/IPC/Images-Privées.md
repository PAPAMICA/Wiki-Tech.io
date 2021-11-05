---
title: Infomaniak Public Cloud - Créer une image personnalisée
description: Créer une image à partir d'une instance et démarrer depuis cette image
published: true
date: 2021-11-05T11:05:05.360Z
tags: openstack, infomaniak, public-cloud, cloud, ipc
editor: markdown
dateCreated: 2021-11-05T11:04:22.782Z
---

![plan_de_travail_1@4x.png](/images/cloud/infomaniak-public-cloud/plan_de_travail_1@4x.png =600x)
# Introduction
Il existe deux manières de créer une image d'une instance pour l'utiliser sur une autre instance : soit par un **snapshot**, soit par un **volume**.
## Snapshot 
Le snapshot est très **utile pour sauvegarder** un état d'une instance ou pour la dupliquer.
- L'image n'est jamais altérée (réutilisable).
- Plusieurs instances peuvent utiliser cette image.
- Très peu coûteux (stockage objet). 
## Volume
Le volume sert principalement à la **migration d'instance**.
- Plus rapide à provisionner.
- Une seule instance sur un volume.
- Coûte plus cher (stockage block).

# Utiliser un snapshot
## Horizon
### Créer un snapshot d'une instance
### Démarrer une instance sur ce snapshot

## CLI
### Créer un snapshot d'une instance
### Démarrer une instance sur ce snapshot
### Lister les snapshots
### Afficher un snapshot
### Supprimer un snapshot

# Utiliser un volume
## Horizon
### Créer un volume d'une instance
### Démarrer une instance sur ce volume
## CLI
### Créer un volume d'une instance
### Démarrer une instance sur ce volume
### Lister les volumes
### Afficher un volume
### Supprimer un volume