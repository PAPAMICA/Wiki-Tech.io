---
title: Jirafeau
description: Alternative opensource et self hosted de Wetransfert.
published: true
date: 2022-04-06T22:33:17.357Z
tags: 
editor: markdown
dateCreated: 2021-05-24T10:34:49.087Z
---

![Cloudron - Install Jirafeau - Select your file, upload, share a link](https://api.cloudron.io/api/v1/apps/net.jirafeau.cloudronapp/versions/1.2.0/icon)

# Présentation

Jirafeau vous permet de partager en 2 clics des fichiers avec d'autres utilisateurs comme WeTransfert. Il apporte quelques fonctionnalités intéressantes:

-   Les téléchargements uniques
-   Un lecteur d'images et de vidéos
-   La possibilité de mettre un mot de passe
-   Le stockage limité dans le temps
-   Une API
-   Le chiffrement des données stockées
-   Et plein de petites configurations utiles !

Vous pouvez retrouver le Gitlab du projet [ici](https://gitlab.com/mojo42/Jirafeau) ainsi que la démo officielle [ici](https://jirafeau.net).

Je met à disposition ce service aussi de mon coté, accessible à cette adresse : [send.papamica.fr](https://send.papamica.fr)

# Installation

Vous pouvez passer par une installation standard via la documentation disponible sur [Gitlab](https://gitlab.com/mojo42/Jirafeau), le déployer directement depuis mon template d'application dans Portainer (tutoriel dispo ici) ou utiliser le docker-compose suivant :

```yaml
version: '2'

services:
  jirafeau:
    image: jgeusebroek/jirafeau
    container_name: jirafeau
    restart: unless-stopped
    volumes:
      - /apps/jirafeau/cfg:/cfg
      - /apps/jirafeau/data:/data
      - /apps/jirafeau/media:/jirafeau/media
    
    # Inutile avec Traefik    
    #ports:
    #  - 80:80
    
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
      - "traefik.http.routers.jirafeau.entrypoints=http"
      - "traefik.http.routers.jirafeau.rule=Host(`<URL>`)"
      - "traefik.http.middlewares.jirafeau-https-redirect.redirectscheme.scheme=https"
      - "traefik.http.routers.jirafeau.middlewares=jirafeau-https-redirect"
      - "traefik.http.routers.jirafeau-secure.entrypoints=https"
      - "traefik.http.routers.jirafeau-secure.rule=Host(`<URL>`)"
      - "traefik.http.routers.jirafeau-secure.tls=true"
      - "traefik.http.routers.jirafeau-secure.tls.certresolver=http"
      - "traefik.docker.network=proxy"
      
networks:
  proxy:
    external:
      name: proxy
```

Votre Jirafeau sera accessible directement depuis l'URL que vous lui avez attribué avec Traefik ou depuis `http://<server>:<port>`.

L'interface administrateur est disponible ici : `<URL>/admin.php`

L'API est disponible ici : `<URL>/script.php`

# Personnalisation

Il est possible de personnaliser le thème de Jirafeau assez simplement.

Voici deux thèmes assez modernes et jolies :

-   [Minuit](https://github.com/wizhou/jirafeau-theme-minuit)
-   [Elegantish](https://github.com/JordyValentine/jirafeau-elegantish)

Je vous laisse consulter la documentation de ces thèmes pour l'installation.