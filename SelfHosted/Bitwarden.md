---
title: Bitwarden (Vaultwarden)
description: Gestionnaire de mot de passe. Alternative opensource et self hosted de Dashlane.
published: true
date: 2021-10-04T16:42:22.643Z
tags: 
editor: markdown
dateCreated: 2021-05-24T10:34:38.332Z
---

![Bitwarden - Wikipedia](https://upload.wikimedia.org/wikipedia/commons/thumb/c/cc/Bitwarden_logo.svg/1200px-Bitwarden_logo.svg.png =x200)

# Présentation

Vaultwarden est un gestionnaire de mot de passe gratuit, OpenSource et SelfHosted basé sur [Bitwarden](https://bitwarden.com). il inclut pas mal de fonctionnalités intéressantes tels que :

-   Une application pour Windows, Mac et Linux ([Disponible ici](https://bitwarden.com/download/))
-   Une application iOS et Android ([Disponible ici](https://bitwarden.com/download/))
-   Une extension de navigateurs ([Disponible ici](https://bitwarden.com/download/))
-   La gestion du 2FA
-   Gestion de notes sécurisés, d'identités et de moyens de payements
-   Classements par collections, dossiers et favoris
-   Un générateur de mot de passe
-   La possibilité de partager des mots de passe avec d'autres utilisateurs internes et externes
-   Une gestion des droits avancées
-   Le chiffrement des données stockées
-   Support de la Yubikey et de Duo
-   Et plein de petites configurations utiles !

Vous pouvez retrouver le Github du projet [ici](https://github.com/dani-garcia/vaultwarden).

# Installation

Vous pouvez passer par une installation standard via la documentation disponible sur [Github](https://github.com/dani-garcia/vaultwarden), le déployer directement depuis mon template d'application dans Portainer (turoriel dispo ici) ou utiliser le docker-compose suivant :

```yaml
version: "2"
services:
  vaultwarden:
    image: vaultwarden/server:latest
    container_name: vaultwarden
    restart: always
    volumes:
      - /apps/vaultwarden/bw-data:/data
      - /etc/localtime:/etc/localtime
    environment:
      WEBSOCKET_ENABLED: 'true' # Required to use websockets
      SIGNUPS_ALLOWED: 'true'   # set to false to disable signups
      TZ: 'Europe/Paris'
      ADMIN_TOKEN: '$TOKEN'
    ports:
      - 3012:3012
      #- 8080:80 # Facultatif avec Traefik
    
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
      - "traefik.http.routers.password.entrypoints=http"
      - "traefik.http.routers.password.rule=Host(`$URL`)"
      - "traefik.http.middlewares.password-https-redirect.redirectscheme.scheme=https"
      - "traefik.http.routers.password.middlewares=password-https-redirect"
      - "traefik.http.routers.password-secure.entrypoints=https"
      - "traefik.http.routers.password-secure.rule=Host(`$URL`)"
      - "traefik.http.routers.password-secure.tls=true"
      - "traefik.http.routers.password-secure.tls.certresolver=http"
      - "traefik.docker.network=proxy"
      
networks:
  proxy:
    external:
      name: proxy
```

> Pensez à changer dans le docker-compose ou à définir les variables suivantes : **TOKEN**, **URL_LOKI** et **URL** en fonction de votre installation.
{.is-warning}

Votre Vaultwarden sera accessible directement depuis l'URL que vous lui aurais attribué avec Traefik ou depuis `http://<server>:<port>`.

L'interface administrateur est disponible ici : `<URL>/admin`

L'API est disponible ici : `<URL>/api`

> Pensez à régler l'heure du serveur hôte pour le 2FA 
{.is-warning}
# Configuration

## L'URL du site

Pour le bon fonctionnement de Vaultwarden, il est indispensable de configurer l'URL du site dans sa configuration :

1.   Rendez-vous sur la panel admin à partir de cette URL : `<URL>/admin` 
2.  Entrez le token que vous avez mis dans votre docker-compose
3.  Cliquez sur l'onglet “Général” dans l'onglet “*Settings*”
4.  Rentrez l'URL de votre site dans le champs “*Domain URL*”

## Le serveur SMTP

Si vous souhaitez utiliser toutes les fonctionnalité de Vaulwarden, vous devez configurer le serveur SMTP :

1.  Rendez-vous sur la panel admin à partir de cette URL : `<URL>/admin` 
2.  Entrez le token que vous avez mis dans votre docker-compose
3.  Cliquez sur l'onglet “*SMTP Email Settings*” dans l'onglet “*Settings*”
4.  Rentrez les informations de votre serveur SMTP
5.  Testez avec l'envoi d'un mail

# Configurer les applications

Les différentes applications sont disponibles au téléchargement [ici](https://bitwarden.com/download/).

Une fois installé, vous devez configurer votre serveur avant de vous connecter :

 1. Cliquez sur l'engrenage en haut à gauche

![](/images/image_2021-05-01_153210.png)

 2. Entrez l'URL de votre serveur et cliquez sur “Enregistrer”

![](/images/image_2021-05-01_153407.png)

 3. Vous pouvez maintenant vous connecter avec vos identifiants dans l'application !