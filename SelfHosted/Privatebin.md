---
title: PrivateBin
description: Le meilleur moyen de partager vos secrets de façon sécurisé et avec une API !
published: true
date: 2021-06-15T10:37:14.769Z
tags: 
editor: markdown
dateCreated: 2021-05-24T10:34:57.623Z
---

![GitHub - PrivateBin/PrivateBin: A minimalist, open source online pastebin  where the server has zero knowledge of pasted data. Data is  encrypted/decrypted in the browser using 256 bits AES.](https://camo.githubusercontent.com/35eb22889fe1705b5a7c3834683b64fbbb191ce4a0735b07cb292c6f0b1bca2c/68747470733a2f2f63646e2e7261776769742e636f6d2f5072697661746542696e2f6173736574732f6d61737465722f696d616765732f707265766965772f6c6f676f536d616c6c2e706e67)

# Présentation

PrivateBin est un pastebin en ligne minimaliste et open source où le serveur n'a aucune connaissance des données collées. Les données sont chiffrées et déchiffrées dans le navigateur à l'aide de l'AES 256 bits en mode compteur Galois. Il s'agit d'un fork de ZeroBin, développé à l'origine par Sébastien Sauvage. ZeroBin a été remanié pour permettre des extensions plus faciles et plus propres. 

PrivateBin a beaucoup plus de fonctionnalités que le ZeroBin original. Il est cependant toujours entièrement compatible avec le schéma de stockage de données ZeroBin. Par conséquent, de telles installations peuvent être mises à niveau vers PrivateBin sans perte de données.

Site : [privatebin.info](https://privatebin.info)

Démo : [privatebin.papamica.com](https://privatebin.papamica.com)

# Installation

Vous pouvez passer par une installation standard via la documentation disponible sur [leur site](https://github.com/PrivateBin/PrivateBin/blob/master/INSTALL.md#installation), le déployer directement depuis mon template d’application dans Portainer (tutoriel dispo ici) ou utiliser le docker-compose suivant : 

```yaml
version: "2"
services:
  # PrivateBin : https://privatebin.info
  privatebin:
    image: jgeusebroek/privatebin
    container_name: privatebin
    restart: always
    volumes:
      - /apps/privatebin/data:/privatebin/data
      - /apps/privatebin/cfg:/privatebin/cfg
    environment:
      hostname: 'privatebin'
      name: 'privatebin'
      
    # Facultatif avec Traefik
    #ports:
      #- 8080:80

    # Facultatif  
    logging:
      driver: loki
      options:
        loki-url: "$URL_LOKI"
        loki-external-labels: service={{.Name}}
    networks:
      - proxy
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.privatebin.entrypoints=http"
      - "traefik.http.routers.privatebin.rule=Host(`$URL`)"
      - "traefik.http.middlewares.privatebin-https-redirect.redirectscheme.scheme=https"
      - "traefik.http.routers.privatebin.middlewares=privatebin-https-redirect"
      - "traefik.http.routers.privatebin-secure.entrypoints=https"
      - "traefik.http.routers.privatebin-secure.rule=Host(`$URL`)"
      - "traefik.http.routers.privatebin-secure.tls=true"
      - "traefik.http.routers.privatebin-secure.tls.certresolver=http"
      - "traefik.docker.network=proxy"
  
networks:
  proxy:
    external:
      name: proxy
```

> Pensez à changer dans le docker-compose ou à définir les variables suivantes : **URL\_LOKI** et **URL** en fonction de votre installation.
{.is-warning}

Votre Privatebin sera accessible directement depuis l’URL que vous lui aurais attribué avec Traefik ou depuis `http://<server>:<port>`.

# API Python

Privatebin met à disposition une API bien utile pour l'intégrer dans vos scripts. La documentation est disponible ici : [Github.com](https://github.com/PrivateBin/PrivateBin/wiki/API)

## Installation

Installez PrivateBinAPI avec la commande suivante : 

```python
pip install PrivateBinAPI
```

Importez la librairie dans vos scripts :

```python
import privatebinapi
```

## Envoyer un Paste

```python
response = privatebinapi.send("https://sitedupaste.com", text="Hello, world!")
```

Réponse :

```json
{
    "deletetoken": "< paste delete token >",
    "full_url": "< direct link to paste> ",
    "id": "< paste ID >",
    "passcode": "< paste passcode >",
    "status": 0,
    "url": "/?< paste ID >"
}
```

## Ajouter une expiration

Il existe un nombre limité de délais d'expiration valides. Vous devez sélectionner l'un des éléments suivants :

```python
("5min", "10min", "1hour", "1day", "1week", "1month", "1year", "never")
```

Par défaut, “`1day`” est sélectionné.

```python
response = privatebinapi.send(
     "https://sitedupaste.com",
     text="Hello, world!",
     expiration="5min"
 )
```

## Ajouter un mot de passe

Configurer un mot de passe à votre Paste est simple :

```python
response = privatebinapi.send(
     "https://sitedupaste.com",
     text="Hello, world!",
     password="Secure123!"
 )
```

## Choisir un format

Il n'y a que trois options valides pour ce paramètre : `"plaintext"`, `"syntaxhighlighting"`, and `"markdown"`.

Par défaut, `"plaintext"` est sélectionné.

```python
response = privatebinapi.send(
     "https://sitedupaste.com",
     text="Hello, world!",
     formatting="markdown"
 )
```

## Supprimer après lecture

Si vous souhaitez qu'un Paste soit supprimé immédiatement après avoir été lu, passez à `True` le paramètre `burn_after_reading`.

```python
response = privatebinapi.send(
     "https://sitedupaste.com",
     text="Hello, world!",
     burn_after_reading=True
 )
```

## Récupérer un Paste

Obtenir un Paste à partir d'une instance de PrivateBin est très simple :

```python
response = privatebinapi.get("https://sitedupaste.com/?fakePasteLink#1234567890")
```

Réponse :

```json
{
    "attachment": {
        "content": b"< attachment content in bytes >",
        "filename": "< name of attachment >"
    },
    "id": '< paste ID >",
    "meta": {
        "created": < UNIX timestamp >,
        "time_to_live": < seconds until deletion >
    },
    "status": 0,
    "text": "< text content of the paste >",
    "url": "/?< paste ID >",
    "v": < encryption version 1 or 2 >}
}
```

## Récupérer un Paste protégé par un mot de passe

Si le Paste est protégé par un mot de passe, utilisez le paramètre `password`.

```python
response = privatebinapi.get(
     "https://sitedupaste.com/?fakePasteLink#1234567890",
     password="PAPAMICA123!"
 )
```

## Supprimer un Paste

Pour supprimer un Paste, vous avez besoin de son URL et de son token de suppression.

```python
response = privatebinapi.delete(
     "https://sitedupaste.com/?fakePasteLink#1234567890",
     "fake1delete2token3"
 )
```