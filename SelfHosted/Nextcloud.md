---
title: NextCloud
description: Un cloud selfhosted avec énormément de fonctionnalités !
published: true
date: 2021-07-19T08:47:08.057Z
tags: 
editor: markdown
dateCreated: 2021-05-24T10:34:55.512Z
---

![](https://open-freax.fr/content/images/size/w960/wordpress/2017/09/Nextcloud_Logo.svg_.png)

# Présentation

# Installation
Docker compose :
```yaml
version: "2"
services:
  # Nextcloud : https://nextcloud.com/
  nextcloud:
    image: nextcloud
    container_name: nextcloud
    restart: always
    depends_on:
      - nextcloud-db
    environment:
      - UID=1000
      - GID=1000
      - UPLOAD_MAX_SIZE=20G
      - APC_SHM_SIZE=128M
      - OPCACHE_MEM_SIZE=128
      - CRON_PERIOD=15m
      - TZ=Europe/Paris
      - DOMAIN=$URL
      - MYSQL_DATABASE=nextcloud
      - MYSQL_USER=nextcloud
      - MYSQL_PASSWORD=$PASSWORD
      - MYSQL_HOST=nextcloud-db
    volumes:
      - /apps/data:/data
      - /apps/nextcloud:/var/www/html
    networks:
      - proxy
   
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.nextcloud.entrypoints=http"
      - "traefik.http.routers.nextcloud.rule=Host(`$URL`)"
      - "traefik.http.middlewares.nextcloud-https-redirect.redirectscheme.scheme=https"
      - "traefik.http.routers.nextcloud.middlewares=nextcloud-https-redirect"
      - "traefik.http.routers.nextcloud-secure.entrypoints=https"
      - "traefik.http.routers.nextcloud-secure.rule=Host(`$URL`)"
      - "traefik.http.routers.nextcloud-secure.tls=true"
      - "traefik.http.routers.nextcloud-secure.tls.certresolver=http"
      - "traefik.http.services.nextcloud-secure.loadbalancer.server.port=80"
      - "traefik.docker.network=proxy"

  # Base de donnée
  nextcloud-db:
    image: mariadb:10.4.12
    container_name: nextcloud-db
    restart: always
    volumes:
      - /apps/nextcloud/db:/var/lib/mysql
    environment:
      - MYSQL_ROOT_PASSWORD=$PASSWORD
      - MYSQL_DATABASE=nextcloud
      - MYSQL_USER=nextcloud
      - MYSQL_PASSWORD=$PASSWORD

    # Facultatif  
    networks:
      - proxy

networks:
  proxy:
    external:
      name: proxy
```

> Pensez à changer dans le docker-compose ou à définir les variables suivantes : **PASSWORD** et **URL** en fonction de votre installation.
{.is-warning}

Votre Nextcloud sera accessible directement depuis l'URL que vous lui aurais attribué avec Traefik ou depuis `http://<server>:<port>`.
# Plugins essentiels