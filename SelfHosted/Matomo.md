---
title: Matomo
description: Enfin une alternative viable à Google Analytics !
published: true
date: 2021-06-14T06:42:40.896Z
tags: 
editor: markdown
dateCreated: 2021-05-24T10:34:53.359Z
---

![Fichier:Matomo Logo.svg — Wikipédia](https://upload.wikimedia.org/wikipedia/commons/thumb/c/c9/Matomo_Logo.svg/1280px-Matomo_Logo.svg.png)

# Présentation

Il s’agit d’une plateforme libre d’analyse du trafic sur le web. L’outil est utilisé par des particuliers, des entreprises et même les gouvernements de différents pays. Matomo vous permet de récupérer des informations à propos de vos utilisateurs. Vous aurez ainsi droit à quelques indicateurs clés de performance concernant les visites, le taux de conversion, les téléchargements, les mots-clés utilisés pour trouver votre site…

Il est également possible d’intégrer à votre site e-commerce pour analyser vos revenus, vos commandes, etc. Vous pouvez même intégrer vos logs serveur dans Matomo pour les afficher et les analyser.

Cet outil est gratuit si vous l’hébergez vous-même sur votre serveur. Sinon vous pouvez souscrire à une offre hébergée par la société qui gère Matomo. Les formules varient de 7.5€ à 41€ par mois. Si vous êtes intéressé par la version payante, [voici le lien.](https://www.innocraft.cloud/#pricing) Sinon vous pouvez passer par la version selfhosted via docker ! (Spoiler, c'est juste en dessous) 

Avec Matomo, vous êtes propriétaire de vos données, et l’outil est en conformité avec les exigences de confidentialité de vos visiteurs. Vous conservez la possibilité de voir le comportement de vos visiteurs sur votre site. La plateforme est personnalisable et extensible grâce à son API. Matomo dispose aussi d’une application mobile. Elle vous permet de suivre en temps réel sur votre smartphone le nombre de visiteurs présents sur votre site. Dernier point important : votre seule limite de données est celle de votre serveur.

Si vous n’êtes pas encore entièrement convaincu, vous pouvez suivre ce lien pour voir en détails [les fonctionnalités](https://matomo.org/what-is-matomo/) de Matomo. Vous avez même la possibilité de le tester, pour avoir une idée du résultat et de la manière dont vous pourrez vous en servir.

Voici le lien vers [la démo](https://demo.matomo.org/index.php?module=CoreHome&action=index&idSite=3&period=day&date=yesterday#?idSite=3&period=day&date=yesterday&category=Dashboard_Dashboard&subcategory=1) de Matomo. Il ne vous reste plus qu’à débrancher Google Analytics de vos sites !

Site officiel : [matomo.org](https://matomo.org)

# Installation

Vous pouvez passer par une installation standard via la documentation disponible sur [leur site](https://fr.matomo.org/docs/installation/), le déployer directement depuis mon template d'application dans Portainer (tutoriel dispo ici) ou utiliser le docker-compose suivant :

```yaml
version: "2"
services:
  # Base de données    
  matomo-db:
    image: linuxserver/mariadb
    container_name: matomo-db
    restart: unless-stopped
    environment:
      - PUID=1000
      - PGID=1000
      - MYSQL_ROOT_PASSWORD=pihvzlbkcgvlknfze!ef6e4
      - TZ=Europe/Paris
      - MYSQL_DATABASE=matomo
      - MYSQL_USER=matomo
      - MYSQL_PASSWORD=Matomo65468431
      # Flag necessary for the database max allowed packet check
      # https://matomo.org/faq/troubleshooting/faq_183/
      - MARIADB_EXTRA_FLAGS=--max_allowed_packet=64MB
    volumes:
      - /apps/matomo/db:/config

    # Facultatif
    networks:
      - proxy
    logging:
      driver: loki
      options:
        loki-url: "$URL_LOKI"
        loki-external-labels: service={{.Name}}
        
  # Matomo : https://fr.matomo.org
  matomo:
    image: matomo
    container_name: matomo
    restart: unless-stopped
    environment:
      - MATOMO_DATABASE_HOST=matomo-db
      - MATOMO_DATABASE_DBNAME=matomo
      - MATOMO_DATABASE_USERNAME=matomo
      - MATOMO_DATABASE_PASSWORD=Matomo65468431
    volumes:
      - /apps/matomo/data:/var/www/html
    depends_on:
      - matomo-db
    # Facultatif avec Traefik  
    #Ports:
    #  - 80:80

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
      - "traefik.http.routers.matomo.entrypoints=http"
      - "traefik.http.routers.matomo.rule=Host(`$URL`)"
      - "traefik.http.middlewares.matomo-https-redirect.redirectscheme.scheme=https"
      - "traefik.http.routers.matomo.middlewares=matomo-https-redirect"
      - "traefik.http.routers.matomo-secure.entrypoints=https"
      - "traefik.http.routers.matomo-secure.rule=Host(`$URL`)"
      - "traefik.http.routers.matomo-secure.tls=true"
      - "traefik.http.routers.matomo-secure.tls.certresolver=http"
      - "traefik.docker.network=proxy"

      
networks:
  proxy:
    external:
      name: proxy
```

> Pensez à changer dans le docker-compose ou à définir les variables suivantes : **URL_LOKI** et **URL** en fonction de votre installation.
{.is-warning}


Votre Matomo sera accessible directement depuis l'URL que vous lui aurais attribué avec Traefik ou depuis `http://<server>:<port>`.  
 

# Configuration

## Site Wordpress

Le plus simple reste de passer par une extension :

 1 - Ajoutez un site dans Matomo

 2 - Connectez vous en administrateur sur votre Wordpress

 3 - Ajoutez et activez l'extension “[**`Tracking code for Matomo, by Sergio Santos`**](https://wordpress.org/plugins/piwik-tracking-by-mente-binaria/)”

 4 - Allez dans les réglages de l'extension

 5 - Activez le code de tracking

 6 - Rentrez l'adresse de votre serveur Matomo

 7 - Activez la compatibilité SSL si vous l'utilisez

 8 - Enregistrez les modifications & Enjoy ! 

## Wiki.js

Dans Wiki.js c'est directement intégré ! Si c'est pas beau la vie !

 1 - Rendez vous dans la partie administrateur de Wiki.js

 2 - Dans la catégorie “*Analytiques*”, sélectionnez “*Matomo*”

 3 - Entrez l'ID de votre site (dispo sur votre serveur Matomo) ainsi que l'URL de votre serveur Matomo

 4 - Cliquez sur “*Appliquer*” & Enjoy ! 

## Site web classique

Après avoir configurer le site dans Matomo, il vous fournis un code que vous pouvez rajouter à votre site directement entre le head et le body. Voici un exemple du code fournit :

```html
<!-- Matomo -->
<script type="text/javascript">
  var _paq = window._paq = window._paq || [];
  /* tracker methods like "setCustomDimension" should be called before "trackPageView" */
  _paq.push(['trackPageView']);
  _paq.push(['enableLinkTracking']);
  (function() {
    var u="//<SERVEUR_MATOMO/";
    _paq.push(['setTrackerUrl', u+'matomo.php']);
    _paq.push(['setSiteId', '6']);
    var d=document, g=d.createElement('script'), s=d.getElementsByTagName('script')[0];
    g.type='text/javascript'; g.async=true; g.src=u+'matomo.js'; s.parentNode.insertBefore(g,s);
  })();
</script>
<!-- End Matomo Code -->
```