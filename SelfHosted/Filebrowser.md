---
title: FileBrowser
description: 
published: true
date: 2021-06-14T06:37:52.143Z
tags: 
editor: markdown
dateCreated: 2021-05-24T10:34:40.448Z
---

![](https://raw.githubusercontent.com/filebrowser/logo/master/banner.png)

# Présentation

Si vous souhaitez partager des fichiers de manière permanente, une interface Web est probablement le plus simple à utiliser pour vos utilisateurs. 

[Filebrowser](https://github.com/filebrowser/filebrowser) est une interface Web pour accéder à des fichiers, elle vous permet de faire la plupart des choses avec les fichiers: télécharger, partager, modifier, etc. 

Nous allons voir les étapes consistant à n'autoriser aucune de ces options et à autoriser uniquement l'accès anonyme en lecture seule à un répertoire à l'aide de l'interface Web de FileBrowser.

# Création du Dockerfile

Le projet FileBrowser met à disposition une [image Docker](https://hub.docker.com/r/filebrowser/filebrowser) avec leur [binaire intégré](https://github.com/filebrowser/filebrowser/blob/master/Dockerfile) directement. Nous devons faire presque la même chose, mais le faire fonctionner sans authentification et fonctionner en tant qu'utilisateur normal au lieu de root (mauvaise pratique).

Dans un fichier [Dockerfile](https://hastebin.papamica.com/ukukomelut), nous commençons par utiliser l'image alpine complète et en copiant le binaire de FileBrowser à partir de l'image docker `filebrowser/filebrowser` :

```DockerFile
FROM filebrowser/filebrowser as fb
FROM alpine:latest
COPY --from=fb /filebrowser /opt/app/filebrowser
```

Nous ne voulons pas que le processus s'exécute en tant que root, nous créons donc un nouvel utilisateur et mettons à jour les certificats SSL :

```DockerFile
RUN apk --update add ca-certificates bash \
    && adduser -h /opt/app -D app
```

Maintenant, pour le script entrypoint : nous avons besoin de lancer FileBrowser avec la configuration souhaitée mais aussi de créer un utilisateur pour l'option `--auth.method='noauth'` (voir l'[issue #700](https://github.com/filebrowser/filebrowser/issues/700))

```bash
#!/bin/bash
./filebrowser config init --port 4000 --address "" --baseurl "" --log "stdout" --root="/srv" --auth.method='noauth' --commands "" --lockPassword --perm.admin=false --perm.create=false --perm.delete=false --perm.execute=false --perm.modify=false --perm.rename=false --signup=false
./filebrowser users add anonymous "anonymous"
exec ./filebrowser
```

Nous pouvons désormais finir notre [Dockerfile](https://hastebin.papamica.com/ukukomelut) :

```DockerFile
COPY entrypoint /opt/app/entrypoint
RUN chmod a+x /opt/app/entrypoint

VOLUME /srv
EXPOSE 4000
USER app
WORKDIR /opt/app


ENTRYPOINT [ "/opt/app/entrypoint" ]
```

Vous retrouverez le Dockerfile complet ici : [Dockerfile](https://hastebin.papamica.com/ukukomelut)

# Création du Docker-Compose

Maintenant que nous avons notre Dockerfile, nous pourrions démarrer notre container avec ces commandes Docker :

```bash
docker build --tag hostfiles .
docker run --detach -p 80:4000 --restart=always --volume /apps/files:/srv:ro hostfiles
```

Mais je préfère toujours avoir un Docker-Compose, voici donc ce fameux sésame :

```yaml
version: '2'

services:
  filebrowser:
    image: hostfiles:latest # Nom de l'image précédemment créée avec la commande "docker build"
    container_name: filebrowser-partage
    restart: unless-stopped
    volumes:
      - /DATA:/srv:ro # Localisation des données à partager
    ports:
      - 80:4000 # Vous pouvez changer le port ici
```

Pour lancer le container :

```bash
docker-compose up -d
```

Vous pouvez maintenant y accéder à l'adresse : `http://<IP_SERVER>:80`

# Résumé

Vous devez avoir cette arborescence de fichiers :

```bash
.
├── docker-compose.yml
├── dockerfile
└── entrypoint
```

Avec :

-   [`docker-compose.yml`](https://hastebin.papamica.com/yelocacilo) ou [`docker-compose.yml`](https://hastebin.papamica.com/nobuduzimu) (avec Traefik et Loki)
-   [`dockerfile`](https://hastebin.papamica.com/ukukomelut)
-   [`entrypoint`](https://hastebin.papamica.com/deruxizeqe)