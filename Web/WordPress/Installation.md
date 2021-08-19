---
title: Installation de WordPress avec Docker
description: Installer une instance WordPress avec Docker !
published: true
date: 2021-06-15T11:00:29.626Z
tags: wordpress, site, docker
editor: markdown
dateCreated: 2021-06-15T10:58:36.489Z
---

![Fichier:WordPress logo.svg — Wikipédia](https://upload.wikimedia.org/wikipedia/commons/thumb/2/20/WordPress_logo.svg/1200px-WordPress_logo.svg.png)

# Présentation

**WordPress** est un système de gestion de contenu (SGC ou *content management system* (CMS) en anglais) gratuit, libre et *open-source*. Ce logiciel écrit en PHP repose sur une base de données MySQL et est distribué par la fondation WordPress.org. Les fonctionnalités de WordPress lui permettent de créer et gérer différents types de sites Web : site vitrine, site de vente en ligne, site applicatif, blog, portfolio, site institutionnel, site d'enseignement…

Il est distribué selon les termes de la licence GNU GPL version 2. Le logiciel est aussi utilisé comme socle du service multi-site WordPress.com, celui-ci supporte plusieurs millions de sites

Site officiel : [wordpress.org](https://wordpress.org)

# Installation

Vous pouvez passer par une installation standard via la documentation disponible sur [leur site](https://wordpress.org/support/article/how-to-install-wordpress/), le déployer directement depuis mon template d’application dans Portainer (tutoriel dispo ici) ou utiliser le docker-compose suivant :

```yaml
version: "2"

services:
   # Base de données
   wordpress-db:
     container_name: wordpress-db
     image: mysql:5.7
     volumes:
       - /apps/wordpress/db:/var/lib/mysql
     restart: always
     environment:
       MYSQL_ROOT_PASSWORD: $DB_ROOT
       MYSQL_DATABASE: wordpress
       MYSQL_USER: $DB_USER
       MYSQL_PASSWORD: $DB_PASSWORD

     #Facultatif  
     networks:
       - proxy
     logging:
       driver: loki
       options:
         loki-url: "$URL_LOKI"
         loki-external-labels: service={{.Name}}

   wordpress:
     container_name: wordpress
     depends_on:
       - wordpress-db
     image: wordpress:latest
     restart: always
     volumes:
       - /apps/wordpress/uploads.ini:/usr/local/etc/php/conf.d/uploads.ini
       - /apps/wordpress/html:/var/www/html
     environment:
       WORDPRESS_DB_HOST: wordpress-db:3306
       WORDPRESS_DB_USER: $DB_USER
       WORDPRESS_DB_PASSWORD: $DB_PASSWORD
       WORDPRESS_DB_NAME: wordpress
    #Facultatif avec Traefik
#    ports:
#     - 8080:80

     # Facultatif
     networks:
       - proxy
     logging:
       driver: loki
       options:
         loki-url: "$URL_LOKI"
         loki-external-labels: service={{.Name}}
     labels:
       - "traefik.enable=true"
       - "traefik.http.routers.wordpress.entrypoints=http"
       - "traefik.http.routers.wordpress.rule=Host(`$URL_WEBSITE`)"
       - "traefik.http.middlewares.wordpress-https-redirect.redirectscheme.scheme=https"
       - "traefik.http.routers.wordpress.middlewares=wordpress-https-redirect"
       - "traefik.http.routers.wordpress-secure.entrypoints=https"
       - "traefik.http.routers.wordpress-secure.rule=Host(`$URL_WEBSITE`)"
       - "traefik.http.routers.wordpress-secure.tls=true"
       - "traefik.http.routers.wordpress-secure.tls.certresolver=http"
       - "traefik.docker.network=proxy"
  
networks:
  proxy:
    external:
      name: proxy
```

> Pensez à changer dans le docker-compose ou à définir les variables suivantes :  **DB\_ROOT**, **DB\_USER**, **DB\_PASSWORD**, **URL\_LOKI** et **URL** en fonction de votre installation.
{.is-warning}


> Votre site WordPress sera accessible directement depuis l’URL que vous lui aurais attribué avec Traefik ou depuis `http://<server>:<port>`.
{.is-success}


# Configuration

Rendez-vous sur l'URL de votre site, vous devriez tomber sur l'écran suivant : 

Sélectionnez la langue :

![](/images/wordpress/installation/wordpress_install_1.png)

Configurez le titre, l'utilisateur et le mail du site :

![](/images/wordpress/installation/wordpress_install_2.png)

Connectez vous avec le compte précédemment créé :

![](/images/wordpress/installation/wordpress_install_3.png)

Vous arrivez directement sur le tableau de bord : `http://URL-DU-SITE/wp-admin`

![](/images/wordpress/installation/wordpress_install_4.png)

Et votre site est lui aussi accessible à l'adresse : `http://URL-DU-SITE/`

![](/images/wordpress/installation/wordpress_install_5.png)