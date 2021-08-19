---
title: Vault
description: Un gestionnaire de secrets avec API !
published: true
date: 2021-06-14T06:46:03.937Z
tags: 
editor: markdown
dateCreated: 2021-05-24T10:34:59.737Z
---

![HashiCorp Vault - AppRole Authentication | Drupal.org](https://www.drupal.org/files/project-images/Vault_VerticalLogo_FullColor_2_0.png)

# Présentation

Vault est - comme son nom l'indique - un coffre-fort pour vos données. Il va vous permettre d'accéder en toute sécurité à vos secrets... Pour rappel, les *secrets,* sont des informations sensibles : comme par exemple un nom d'utilisateur et son mot de passe ou même encore des fichiers, par exemple des clés SSH.

Globalement un *secret* est une information dont vous souhaitez contrôler l'accès.

Vault est donc un outil qui fournit une interface unifiée pour toutes vos informations sensibles et donc centralise l'information :

Plus besoin de chercher votre mot de passe `root` de base de données pendant des heures ! 🤬

Et d'en arriver à prier pour qu'il se trouve dans le fichier `configuration.php` ... ( de toute façon vous ne mettez jamais l'accès `root` pour vos applications?non?! )

De plus avec Vault, on va garantir un contrôle d'accès strict aux données sensibles avec en autre une gestion des droits et accès utilisateurs, mais aussi la tenue d'un d'un journal d'audit détaillé.

Il s'agit vraiment d'un outil très complet, qui permet encore plus de choses ( extrait traduit de la documentation officielle ) :

> -   Stockage secret sécurisé: Vault crypte les secrets avant de les écrire dans le stockage persistant, donc accéder au stockage brut ne suffit pas pour accéder à vos secrets.
> -   Secrets dynamiques: Vault peut générer des secrets à la demande pour certains systèmes, tels que les bases de données AWS ou SQL. Après avoir créé ces secrets dynamiques, Vault les révoquera également automatiquement une fois le bail terminé.
> -   Chiffrement des données: Vault peut chiffrer et déchiffrer les données sans les stocker. Cela permet aux équipes de sécurité de définir des paramètres de chiffrement et aux développeurs de stocker des données chiffrées dans un emplacement tel que SQL sans avoir à concevoir leurs propres méthodes de chiffrement.
> -   Location et renouvellement: tous les secrets de Vault sont associés à un bail. À la fin du bail, Vault révoquera automatiquement ce secret. Les clients peuvent renouveler les baux via des API de renouvellement intégrées.
> -   Révocation: Vault prend en charge la révocation des secrets. L'outil peut révoquer non seulement des secrets uniques, mais aussi un arbre entier de secrets. Par exemple tous les secrets lus par un utilisateur spécifique, ou tous les secrets d'un type particulier.

À noter que Vault propose plusieurs solutions de stockage des secrets: sur le disque, en mémoire ou bien dans une base *key value (KV)* comme `Consul`.

Comme tous les outils complets, la première prise en main et sa découverte peut s'avérer parfois complexe.

# Installation

Il faut commencer par créer le fichier de configuration `vault.json` dans `/apps/vault/config/` :

```json
{
  "backend": {
    "file": {
      "path": "/vault/file"
    }
  },
  "listener": {
    "tcp":{
      "address": "0.0.0.0:8200",
      "tls_disable": 1
    }
  },
  "ui": true
}
```

Puis créer le `docker-compose.yml` et lancer Vault avec la commande `docker-compose up -d` :

```yaml
version: '2'
services:
  vault:
    image: vault
    container_name: vault
    restart: always
    volumes:
      - /apps/vault/logs:/vault/logs
      - /apps/vault/file:/vault/file
      - /apps/vault/config:/vault/config
    cap_add:
      - IPC_LOCK
    entrypoint: vault server -config=/vault/config/vault.json
    
    #Facultatif avec Traefik
    #ports:
    #  - "8200:8200"

    # Facultatif : support de Loki
    logging:
      driver: loki
      options:
        loki-url: "$URL_LOKI"
        loki-external-labels: service={{.Name}}
    
    # Facultatif : support de Traefik
    networks:
      - proxy
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.vault.entrypoints=http"
      - "traefik.http.routers.vault.rule=Host(`$URL`)"
      - "traefik.http.middlewares.vault-https-redirect.redirectscheme.scheme=https"
      - "traefik.http.routers.vault.middlewares=$SERVICE-https-redirect"
      - "traefik.http.routers.vault-secure.entrypoints=https"
      - "traefik.http.routers.vault-secure.rule=Host(`$URL`)"
      - "traefik.http.routers.vault-secure.tls=true"
      - "traefik.http.routers.vault-secure.tls.certresolver=http"
      - "traefik.http.services.vault-secure.loadbalancer.server.port=8200"
      - "traefik.docker.network=proxy"

networks:
  proxy:
    external:
      name: proxy
```

> Pensez à changer dans le docker-compose ou à définir les variables suivantes : **URL_LOKI** et **URL** en fonction de votre installation.
{.is-warning}

Votre Vault sera accessible directement depuis l'URL que vous lui aurais attribué avec Traefik ou depuis `http://<server>:<port>`.

# Utilisation

## Récupérer un mot de passe avec l'API

### Les commandes

Il va nous falloir plusieurs données pour récupérer un mot de passe :

-   VAULT\_URL : L'URL de votre serveur VAULT (exemple : "*https://vault.papamica.com*")
-   VAULT\_ENGINE : Correspond à l'engine racine de votre dossier contenant vos secrets
-   VAULT\_ROLE : Correspond au nom du dossier qui contient les secrets
-   VAULT\_SECRET\_ID : Correspond à l'ID récupéré au préalable avec la commande `vault write -force auth/approle/role/<VAULT_ROLE>/secret-id`
-   VAULT\_SECRET\_NAME : Correspond au nom du secret

Récupération du VAULT\_TOKEN pour accéder aux secrets :

```bash
VAULT_TOKEN=$(curl -sSf --data "{\"role_id\":\"<ROLE_ID>\",\"secret_id\":\"$VAULT_SECRET_ID\"}" $VAULT_URL/v1/auth/approle/login | jq -r '.["auth"]["client_token"]')
```

Récupération des secrets dans le dossier en Json ;

```bash
curl -sSf -X GET -H "Accept: */*" -H "X-Vault-Token: $VAULT_TOKEN" "$VAULT_URL/v1/$VAULT_ENGINE/data/approle/$VAULT_ROLE"
```

Récupération du secret recherché avec jq :

```bash
 | jq -r '.["data"]["data"]["$VAULT_SECRET_NAME"]'
```

### Récupération des secrets dans un script :

#### Variable d'environnement à configurer

Afin de ne pas dévoiler les informations ou les tokens utilisés, on les met en variable d'environnement :

```bash
export VAULT_URL='' # Vault URL with "https://"
export VAULT_ENGINE='' # Wallet name (ex : VPS)
export VAULT_ROLE='' # Role name (folder)
export VAULT_SECRET_ID='' # To be retrieved in Vault CLI with 'vault write -force auth/approle/role/<VAULT_ROLE>/secret-id'
export VAULT_SECRET_NAME='' # Secret name
```

#### Fonction Bash

```bash
#!/bin/bash

function Get-Secret {
    VAULT_TOKEN=$(curl -sSf --data "{\"role_id\":\"$VAULT_ROLE\",\"secret_id\":\"$VAULT_SECRET_ID\"}" $VAULT_URL/v1/auth/approle/login | jq -r '.["auth"]["client_token"]')
    SECRET=$(curl -sSf -X GET -H "Accept: */*" -H "X-Vault-Token: $VAULT_TOKEN" "$VAULT_URL/v1/$VAULT_ENGINE/data/approle/$VAULT_ROLE" | jq -r --arg VAULT_SECRET_NAME "$VAULT_SECRET_NAME" '.["data"]["data"][$VAULT_SECRET_NAME]')
    echo "$SECRET"
}

VAULT_SECRET_NAME='kaypair'
PASSWORD=$(Get-Secret)
echo $PASSWORD
```

#### Fonction Python

```python
#!/usr/bin/env python
import requests
import os
import json

# Variables declaration
VAULT_URL = os.getenv('VAULT_URL')
VAULT_ENGINE = os.getenv('VAULT_ENGINE')
VAULT_ROLE = os.getenv('VAULT_ROLE')
VAULT_SECRET_ID = os.getenv('VAULT_SECRET_ID')

def GetSecret():
    # Get token access
    data = {"role_id":VAULT_ROLE,"secret_id":VAULT_SECRET_ID}
    response = requests.post(VAULT_URL + '/v1/auth/approle/login', data=data)
    JSON = json.loads(response.text)
    TOKEN = JSON["auth"]["client_token"]
    # Get secret
    headers = {
        'Accept': '*/*',
        'X-Vault-Token': TOKEN
    }
    response = requests.get(VAULT_URL + '/v1/' + VAULT_ENGINE + '/data/approle/' + VAULT_ROLE, headers=headers)
    JSON = json.loads(response.text)
    SECRET = JSON["data"]["data"][VAULT_SECRET_NAME]
    return SECRET

VAULT_SECRET_NAME='kaypair'
SECRET = GetSecret()
print (SECRET)
```