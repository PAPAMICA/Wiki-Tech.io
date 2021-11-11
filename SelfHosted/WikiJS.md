---
title: Wiki.js
description: Un jolie wiki autohébergé !
published: true
date: 2021-10-03T16:10:24.432Z
tags: 
editor: markdown
dateCreated: 2021-05-24T14:15:33.855Z
---

![](https://cdn-images.threadless.com/threadless-media/artist_shops/shops/wikijs/profile/logo-1531876777-e927870eea78296b4aa681910b70a189.png?v=3&d=eyJvbmx5X21ldGEiOiBmYWxzZSwgImZvcmNlIjogZmFsc2UsICJvcHMiOiBbWyJyZXNpemUiLCBbNjAwLCAyNTBdLCB7fV1dfQ==)

# Présentation
Wiki.js est un outils selfhosted développé en javascript qui permet de mettre en place simplement un système de wiki puissant, complet et jolie.
L'un des gros avantages de ce dernier c'est la synchronisation avec git directement pour la sauvegarde et le versionning.
Wiki-Tech.io fonctionne avec Wiki.js depuis ses débuts sous Docker !
La version 3.0 devrais sortir d'ici la fin d'année avec beaucoup de nouveautés:

Site officiel : https://js.wiki
Documentation : https://docs.requarks.io

# Installation
## Linux

1. Téléchargez la dernière version de Wiki.js:
  ```bash
  wget https://github.com/Requarks/wiki/releases/download/2.5.219/wiki-js.tar.gz
  ```
2. Décompressez l'archive dan s le dossier final de l'application :
  ```bash
  mkdir wiki
  tar xzf wiki-js.tar.gz -C ./wiki
  cd ./wiki
  ```
3. Renommez le fichier `config.sample.yml` en `config.yml` :
  ```bash
  mv config.sample.yml config.yml
  ```
4. Editez le fichier de config et remplissez les informations de votre base de données et de port : ([Configuration Reference](/install/config)):
  ```bash
  nano config.yml
  ```
5. ***Pour l'installation avec SQLite:*** *(ignorez cette etape sinon)* Récupère les liaisons natives pour SQLite3 :
  ```bash
  npm rebuild sqlite3
  ```
6. Lancez Wiki.js
  ```bash
  node server
  ```
7. Attendez que vous soyez invité à ouvrir la page de configuration dans votre navigateur.
8. Complétez l'assistant de configuration pour terminer l'installation.

### Run as service

Il existe plusieurs solutions pour exécuter Wiki.js en tant que service en arrière-plan. Nous allons nous concentrer sur **systemd** dans ce guide car il est disponible dans presque toutes les distributions linux.

1. Créez un nouveau fichier nommé `wiki.service` dans le répertoire `/etc/systemd/system`.
  ```bash
  nano /etc/systemd/system/wiki.service
  ```
2. Collez le contenu suivant (en supposant que votre wiki est installé dans `/var/wiki`) :
  ```ini
  [Unit]
  Description=Wiki.js
  After=network.target

  [Service]
  Type=simple
  ExecStart=/usr/bin/node server
  Restart=always
  # Consider creating a dedicated user for Wiki.js here:
  User=nobody
  Environment=NODE_ENV=production
  WorkingDirectory=/var/wiki

  [Install]
  WantedBy=multi-user.target
  ```
3. Sauvegardez le fichier (<kbd>CTRL</kbd>+<kbd>X</kbd>, suivis de <kbd>Y</kbd>).
4. Redémarrez systemd :
  ```bash
  systemctl daemon-reload
  ```
5. Lancez le service :
  ```bash
  systemctl start wiki
  ```
6. Activez le service au démarrage :
  ```bash
  systemctl enable wiki
  ```

> Vous pouvez consultez les logs du service avec `journalctl -u wiki`
{.is-info}

## Docker
Vous pouvez directement utiliser mon template d'application avec Portainer disponible [ici](https://github.com/PAPAMICA/docker-compose-collection) ou le lancer avec le docker compose suivant :
```yaml
version: "2"
services:

  wiki-db:
    image: postgres:11-alpine
    container_name: wiki-db
    environment:
      POSTGRES_DB: wiki
      POSTGRES_PASSWORD: $DB_PASSWORD
      POSTGRES_USER: $DB_USER
    restart: unless-stopped
    volumes:
      - $DATA_LOCATION/db:/var/lib/postgresql/data
      
    # Facultatif  
    networks:
      - proxy
    logging:
      driver: loki
      options:
        loki-url: "$URL_LOKI"
        loki-external-labels: service={{.Name}}

  wiki:
    image: requarks/wiki:2
    container_name: wiki
    depends_on:
      - wiki-db
    environment:
      DB_TYPE: postgres
      DB_HOST: wiki-db
      DB_PORT: 5432
      DB_USER: $DB_USER
      DB_PASS: $DB_PASSWORD
      DB_NAME: wiki
    restart: unless-stopped

    healthcheck:
      test: curl --fail http://localhost:3000 || exit 1
      interval: 1m
      timeout: 30s
      retries: 3

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
      - "traefik.http.routers.wiki.entrypoints=http"
      - "traefik.http.routers.wiki.rule=Host(`$URL`)"
      - "traefik.http.middlewares.wiki-https-redirect.redirectscheme.scheme=https"
      - "traefik.http.routers.wiki.middlewares=$SERVICE-https-redirect"
      - "traefik.http.routers.wiki-secure.entrypoints=https"
      - "traefik.http.routers.wiki-secure.rule=Host(`$URL`)"
      - "traefik.http.routers.wiki-secure.tls=true"
      - "traefik.http.routers.wiki-secure.tls.certresolver=http"
      - "traefik.http.services.wiki-secure.loadbalancer.server.port=3000"
      - "traefik.docker.network=proxy"
      
networks:
  proxy:
    external:
      name: proxy
```
> Pensez à changer dans le docker-compose ou à définir les variables suivantes : **DB_USER**, **DB_PASSWORD**, **URL_LOKI** et **URL** en fonction de votre installation.
{.is-warning}


# Configuration
## Configurer Git
Pour l'activer il vous suffit de vous rendre dans la catégorie stockage pour activer Git.
![image_2021-10-03_180716.png](/images/selfhosted/wikijs/image_2021-10-03_180716.png)
Une fois activé, il ne vous reste plus qu'à rentrer vos informations de connexions à votre git !

## Masquer la table des matières
Pour masquer la table des matières dans certaines pages, vous pouvez utiliser ce petit bout de CSS pour la page en question :
![wiki_wikijs_matiere.png](/images/selfhosted/wikijs/wiki_wikijs_matiere.png)
```css
.page-col-sd {
  margin-top: 0;
  align-self: flex-start;
  position: sticky;
  top: 64px;
  max-height: calc(100vh - 64px);
  overflow-y: auto;
  -ms-overflow-style: none;
  display: none
}
.flex.lg9 {
  flex-basis: 100%!important;
  flex-grow: 0;
  max-width: 100%!important
}
```