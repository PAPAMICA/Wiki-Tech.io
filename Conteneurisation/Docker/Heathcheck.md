---
title: Healthcheck
description: S’assurer du bon fonctionnement de ses containers !
published: true
date: 2022-05-13T13:33:50.088Z
tags: 
editor: markdown
dateCreated: 2021-05-24T10:34:19.750Z
---

![HealthCheck](https://static1.squarespace.com/static/5e8f3a0561b8203ce00e89de/t/5ef36831fdac3120d5c11a7f/1605732226441/)

# Présentation

Le mécanisme de healthcheck n'est pas une nouveauté dans Docker... Présent depuis la version 1.12 ce mécanisme reste pourtant peu utilisé...

Tout d'abord l'instruction `HEALTHCHECK` , c'est quoi ?

Elle indique à Docker comment tester votre container pour vérifier qu'il fonctionne toujours correctement ( Oui oui, j'ai réussi à vous traduire la documentation officielle ) :

> The `HEALTHCHECK` instruction tells Docker how to test a container to check that it is still working. This can detect cases such as a web server that is stuck in an infinite loop and unable to handle new connections, even though the server process is still running.

Nous avions déjà pu voir comment relancer automatiquement un container dont le processus principal ( vous savez le programme qui prend le `PID 1` dans votre container ) ne fonctionne plus, et ceci à l'aide de l'instruction `restart.`

Mais voilà, dans votre malheur le `PID 1` de votre instance est toujours actif mais pourtant il ne remplit plus son rôle :

Par exemple `nginx` est toujours *UP* mais il ne dessert plus vos pages correctement et/ou vous remonte une erreur ***404/503*** !

  
Dans ce moment là, plusieurs solutions :

-   La supervision finit par vous remonter l'information et vous intervenez ( de façon automatique ou non ),
-   On intègre un healthcheck à notre service pour vérifier sa bonne santé et ainsi avoir l'information en temps réel.

# Créer un healthcheck

Il est possible de déclarer un `HEALTHCHECK` de deux façons :

-   Dans votre fichier `Dockerfile` avant de build votre image,
-   Lors de la déclaration du service dans le fichier docker-compose.

Plutôt que de long discours, voyons par l'exemple la différence entre ces deux types de déclarations.

## Dans un dockerfile

Voici un exemple de fichier `Dockerfile` qui va construire une image custom du CMS `Ghost`:

```Docker
FROM ghost:3

RUN apt update && apt install curl -y \
        && rm -rf /var/lib/apt/lists/*
        
HEALTHCHECK --interval=1m --timeout=30s --retries=3 CMD curl --fail http://localhost:2368 || exit 1
```

🚩 Alors oui il est nécessaire d'installer `curl` qui n'est pas présent dans l'image. Pour les personnes qui souhaitent pousser la réflexion plus loin, voici un [article très intéressant](https://blog.sixeyed.com/docker-healthchecks-why-not-to-use-curl-or-iwr/). 🚩

Voici la liste des options qu'il est possible d'ajouter avant `*CMD*` :

```bash
--interval=DUREE (default: 30s)
--timeout=DUREE (default: 30s)
--start-period=DUREE (default: 0s)
--retries=N (default: 3)
```

`Ghost` étant une application web, l'utilisation de `curl` ou `wget` comme commande de vérification vient immédiatement à l'esprit.

Ici on vérifie qu'une page web est présente et renvoie un code de retour `200` à l'adresse [`http://localhost:2368`](http://localhost:2368/).

On va pouvoir construire notre image :

```bash
docker build -t ghost:healthcheck .
```

Et lancer notre container :

```bash
docker run -d --name some-ghost -p 2368:2368 ghost:healthcheck
```

On peut vérifier l'état de notre avec la commande `docker ps` :

```bash
docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                           PORTS                    NAMES
a3435a4d95fd        ghost:healthcheck   "docker-entrypoint.s…"   3 seconds ago       Up 1 second (health: starting)   0.0.0.0:2368->2368/tcp   some-ghost
```

L'instruction `HEALTHCHECK` peut renvoyer 3 codes de retour :

```bash
0: success - the container is healthy and ready for use
1: unhealthy - the container is not working correctly
2: reserved - do not use this exit code
```

Vous allez constaster visuellement de votre côté, 3 états possibles :

-   **Starting**: Votre container est en cours de démarrage.
-   **Healthy**: La commande de check renvoie un *success.* Votre container est fonctionnel.
-   **Unhealthy**: Votre container ne fonctionne pas correctement !

On peut constater lors de mon `docker ps` que notre container est toujours en cours de démarrage : nous avons eu un retour `starting`.

Vérifions de nouveau quelques secondes plus tard :

```bash
docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS                        PORTS                    NAMES
a3435a4d95fd        ghost:healthcheck   "docker-entrypoint.s…"   About a minute ago   Up About a minute (healthy)   0.0.0.0:2368->2368/tcp   some-ghost
```

Il est donc relativement simple d'ajouter ce mécanisme à votre image !

Toutefois le principal problème lié à cette méthode, est que la vérification est alors générique. Il peut être nécessaire de rendre vos checks plus "personnels", par container.

Il existe d'ailleurs un second inconvénient : votre image n'est peut-être tout simplement pas prévu "*seulement"* pour Docker. Kubernetes intègre par exemple ses propres mécanismes, et vous souhaiterez probablement les utiliser...

## Dans un docker-compose

Dans ce cas, vous pouvez réaliser la même vérification dans le fichier `docker-compose`.

🚩 Pour cet exemple, je vais tout de même construire une image. Sans `HEALTHCHECK`, mais avec l'installation de `curl` 🚩 :

```Docker
FROM ghost:3

RUN apt update && apt install curl -y \
        && rm -rf /var/lib/apt/lists/*
```

et donc construire cette image :

```bash
docker build -t ghost:curl .
```

Les instructions sont similaires à la méthode vu précédemment :

```yaml
version: '3.8'
services:
    ghost:
        image: ghost:curl
        ports:
            - 2368:2368
        healthcheck:
            test: ["CMD", "curl -f http://localhost:2368 || exit 1"]
            timeout: 30s
            interval: 1m
            retries: 3
```

Le fonctionnement est identique à celui que nous venons de voir lors d'une utilisation de l'instruction au sein d'un fichier `Dockerfile`. ( ouf 😂 )

Enfin sachez qu'il est tout simplement possible de désactiver dans votre `docker-compose` un healthcheck créé dans une image à l'aide de l'instruction suivante :

```yaml
healthcheck:
  disable: true
```
Source : [grottedubarbu.fr](https://www.grottedubarbu.fr/docker-healthcheck/)

# Listes de Heathchecks

## MariaDB / MySQL
```yaml
    healthcheck:
      test: ["CMD", "mysqladmin" ,"ping", "-h", "localhost"]
      timeout: 20s
      retries: 10
```

## PostgreSQL
```yaml
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U $DB_USER"]
      interval: 10s
      timeout: 5s
      retries: 5
```
## Curl
```yaml
    healthcheck:
      test: curl --fail http://localhost:80 || exit 1
      interval: 1m
      timeout: 30s
      retries: 3
```
## Wget
```yaml
    healthcheck:
      test: wget -nv -t1 --spider 'http://localhost:80'
      interval: 1m
      timeout: 30s
      retries: 3
 ```
## Sans curl ou wget
```yaml
healthcheck:
  test: ["CMD", "bash", "-c", "exec 5<>/dev/tcp/127.0.0.1/8000"]
  interval: 5s
  timeout: 5s
  retries: 3
```

```yaml
healthcheck:
  test: ["CMD-SHELL", "apt-get update -y && apt-get install -y curl && curl --fail http://localhost:8000 || exit 1"]
  interval: 5s
  timeout: 3s
  retries: 5
  ```
