---
title: Hastebin
description: Alternative opensource et self hosted de Pastebin.
published: true
date: 2021-06-14T06:38:44.694Z
tags: 
editor: markdown
dateCreated: 2021-05-24T10:34:46.926Z
---

![OpenNMS Toolbox](https://toolbox.opennms.eu/logos/hastebin.png)

# Présentation

Hastebin vous permet de partager avec une interface et un fonctionnement des plus simple en 2 clics du texte avec d'autres utilisateurs comme Pastebin.

Vous pouvez retrouver le [Github](https://github.com/seejohnrun/haste-server) du projet [ici](https://github.com/seejohnrun/haste-server) ainsi que le démo officielle [hastebin.com](https://hastebin.com).

Je me à disposition ce service aussi de mon coté, accessible à cette adresse : [hastebin.papamica.fr](https://hastebin.papamica.com)

# Installation

Vous pouvez passer par une installation standard via la documentation disponible sur [Github](https://github.com/seejohnrun/haste-server), le déployer directement depuis mon template d'application dans Portainer (turoriel dispo ici) ou utiliser le docker-compose suivant :

```yaml
version: "2"
services:
	
  hastebin:
    image: rlister/hastebin
    container_name: hastebin
    restart: always
    environment:
      STORAGE_TYPE: file
    volumes:
      - /apps/hastebin/data:/data
      
     # Facultatif avec Traefik 
#    ports:
#      - 8080:80
    
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
      - "traefik.http.routers.hastebin.entrypoints=http"
      - "traefik.http.routers.hastebin.rule=Host(`<URL>`)"
      - "traefik.http.middlewares.hastebin-https-redirect.redirectscheme.scheme=https"
      - "traefik.http.routers.hastebin.middlewares=hastebin-https-redirect"
      - "traefik.http.routers.hastebin-secure.entrypoints=https"
      - "traefik.http.routers.hastebin-secure.rule=Host(`<URL>`)"
      - "traefik.http.routers.hastebin-secure.tls=true"
      - "traefik.http.routers.hastebin-secure.tls.certresolver=http"
      - "traefik.docker.network=proxy"
      
networks:
  proxy:
    external:
      name: proxy
```

Votre Hastebin sera accessible directement depuis l'URL que vous lui aurais attribué avec Traefik ou depuis `http://<server>:<port>`.