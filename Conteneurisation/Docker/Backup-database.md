---
title: Sauvegarde de base de données conteuneurisée
description: Faire un dump d'un base de données fonctionnant dans un conteuneur Docker
published: true
date: 2022-05-10T08:07:32.137Z
tags: docker, backup, sauvegarde, postgres, mysql, mariadb
editor: markdown
dateCreated: 2022-05-10T08:07:32.137Z
---

![logo](https://www.benlcollins.com/wp-content/uploads/2018/11/postgresql-logo.png)
# Postgres
## Récupération des informations
Afin de pouvoir sauvegarder proprement votre base de données (avec un dump), il vous faut les informations suivante que vous pouvez récupérer directement depuis les variables d'envirionnement du conteneur :

### L'utilisateur de la base de données
```bash
docker exec <CONTAINER_NAME> bash -c 'echo "$POSTGRES_USER"'
```

### Le mot de passe de l'utilisateur
```bash
docker exec <CONTAINER_NAME> bash -c 'echo "$POSTGRES_PASSWORD"'
```

## Sauvegarde
```bash
docker exec -t <CONTAINER_NAME> pg_dumpall -c -U <DB_USER> > dump.sql
```

## Restauration
```bash
cat dump.sql | docker exec -i <CONTAINER_NAME> psql -U <DB_USER>
```

![logo](https://upload.wikimedia.org/wikipedia/commons/thumb/6/68/Mariadb-seal-browntext.svg/2560px-Mariadb-seal-browntext.svg.png)
# MySQL / MariaDB

## Récupération des informations
Afin de pouvoir sauvegarder proprement votre base de données (avec un dump), il vous faut les informations suivante que vous pouvez récupérer directement depuis les variables d'envirionnement du conteneur :

### L'utilisateur de la base de données
```bash
docker exec <CONTAINER_NAME> bash -c 'echo "$MYSQL_USER"'
```

### Le mot de passe de l'utilisateur
```bash
docker exec <CONTAINER_NAME> bash -c 'echo "$MYSQL_PASSWORD"'
```

### Le nom de la base de données
```bash
docker exec <CONTAINER_NAME> bash -c 'echo "$MYSQL_DATABASE"'
```
## Sauvegarder
### Une seule table
```bash
docker exec -e MYSQL_PWD=<DB_PASSWORD> <CONTAINER_NAME> /usr/bin/mysqldump -u <DB_USER> --no-tablespaces <DATABASE_NAME> > dump.sql
```
### Toutes les tables
```bash
docker exec <CONTAINER_NAME> sh -c 'exec mysqldump --all-databases -u <DB_USER> -p <DB_PASSWORD> <DATABASE_NAME>' > dump.sql
```
## Restauration
```bash
docker exec -i <CONTAINER_NAME> sh -c 'exec mysql -u <DB_USER> -p <DB_PASSWORD> <DATABASE_NAME>' < dump.sql
```