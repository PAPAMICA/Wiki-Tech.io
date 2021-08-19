---
title: Portainer
description: Une interface web pour gérer Docker !
published: true
date: 2021-06-14T07:35:45.719Z
tags: 
editor: markdown
dateCreated: 2021-05-24T10:34:27.557Z
---

![Les couches logicielles utilisées [LinuQ: Logiciels libres à Québec]](https://linuq.org/_media/projets/portainer_io.png)

# Présentation

Docker est un outil utilisable en ligne de commande, pour le coup c’est pas hyper agréable à utiliser au quotidien malgré que cela soit tout à fait possible. Mais pour une intervention rapide, l'utilisation d'une interface web qui permette de gérer les opérations courantes sur ses conteneurs est très utile. Et ça tombe bien, il existe Portainer pour ça et manager facilement vos Dockers ! Portainer s’installe comme un conteneur docker pour simplifier sont déploiement. Portainer permet de gérer une bonne partie des éléments de docker : conteneurs, images, volumes, réseaux, utilisateurs, etc. Il peux aussi contrôler un autre serveur Docker à distance grâce à un agent et permet de déployer des applications dans des conteneurs en juste quelques clics.

# Installation

## Docker

Une simple commande permet de lancer un container avec Portainer : 

```bash
docker run -d -p 9000:9000 -v /var/run/docker.sock:/var/run/docker.sock -v /apps/portainer:/data portainer/portainer
```

## Docker-compose

Dans mon cas je préfère toujours avoir un docker-compose, je trouve cela bien plus pratique !

```yaml
version: '2'
  
services:
  portainer:
    image: portainer/portainer-ce:latest
    container_name: portainer
    restart: unless-stopped
    security_opt:
      - no-new-privileges:true
    environment: # Ajout de mes templates
      TEMPLATES: https://github.com/PAPAMICA/docker-compose-collection/blob/master/templates-portainer.json
    volumes:
      - /etc/localtime:/etc/localtime:ro
      - /var/run/docker.sock:/var/run/docker.sock:ro
      - /apps/portainer/data:/data
      
    # Facultatif avec Traefik  
    #ports:
    # - 9000:9000
    
    # Facultatif : Support de Loki
    logging:
      driver: loki
      options:
        loki-url: "$URL_LOKI"
        loki-external-labels: service={{.Name}}
    
    # Facultatif : Support de Traefik
    networks:
      - proxy  
    labels:
      - traefik.enable=true
      - traefik.http.routers.portainer.entrypoints=http
      - traefik.http.routers.portainer.rule=Host("$URL")
      - traefik.http.middlewares.portainer-https-redirect.redirectscheme.scheme=https
      - traefik.http.routers.portainer.middlewares=portainer-https-redirect
      - traefik.http.routers.portainer-secure.entrypoints=https
      - traefik.http.routers.portainer-secure.rule=Host("$URL")
      - traefik.http.routers.portainer-secure.tls=true
      - traefik.http.routers.portainer-secure.tls.certresolver=http
      - traefik.http.routers.portainer-secure.service=portainer
      - traefik.http.services.portainer.loadbalancer.server.port=9000
      - traefik.docker.network=proxy
    
networks:
  proxy:
    external: true
```

> Pensez à changer dans le docker-compose ou à définir les variables suivantes : **URL_LOKI** et **URL** en fonction de votre installation.
{.is-warning}


Votre Portainer sera accessible directement depuis l'URL que vous lui aurais attribué avec Traefik ou depuis `http://<server>:<port>`.

# Configuration

## Connexion  au serveur hôte

Une fois installer, il faut créer le compte administrateur et connecter Portainer à la socket Docker : vu que nous donnons au container un accès direct à la socket (via le volume bind), Portainer à d'office accès et peux contrôler Docker de notre serveur hôte. Choisissez donc “***Socket***” et cliquez sur “***Valider***”.

## Connexion à un serveur externe avec l'agent

Il est possible de contrôler un autre serveur sur lequel Docker est installé grâce à un agent Portainer qui se déploie lui aussi dans un container en une simple commande :

```bash
docker run -d -p 9001:9001 --name portainer_agent --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v /var/lib/docker/volumes:/var/lib/docker/volumes portainer/agent
```

Pour ajouter le serveur à notre Portainer, rendez vous dans “***Settings > Endpoints > + Add endpoint***” :

![](/images/image_2021-05-03_213224.png)

Et configurez votre serveur de la sorte : 

![](/images/image_2021-05-03_213454.png)

Et voilà ! Votre serveur sera maintenant disponible dans Portainer !

## Ajouter des modèles d'applications

Etant un gars super top génial (merci de ne pas me contredire.), j'ai fait un modèle de toutes les applications que j'utilise au quotidien… Et tout ça intégré directement dans Portainer pour déployer les applications en juste quelques clics. Vous ai-je dis que ces applications supportent Traefik et Loki ? Genial non ? Allez, allons y !

Il suffit simplement de mettre l'url de mon repo dans la configuration : `https://raw.githubusercontent.com/PAPAMICA/docker-compose-collection/master/templates-portainer.json`

![](https://camo.githubusercontent.com/576de2c1f125016f37a6ddfb36546e78cfb49cd8a61ce8c156e329c723c424f0/68747470733a2f2f692e696d6775722e636f6d2f4d34397373434e2e706e67)

Et maintenant rendez-vous directement dans l'onglet “App Templates” pour découvrir la liste des applications disponibles :

![](/images/image_2021-05-03_211850.png)

## Connexion avec Keycloak

Vous retrouverez ici l'installation de Keycloak : [/SelfHosted/Keycloak](/SelfHosted/Keycloak)

La configuration de l'authentification avec Keycloak se fait directement dans les paramètres comme ceci :

![](/images/image_2021-05-03_212514.png)