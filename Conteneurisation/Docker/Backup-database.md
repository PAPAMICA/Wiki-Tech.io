---
title: Sauvegarde de base de données conteneurisée
description: Faire un dump d'un base de données fonctionnant dans un conteneur Docker
published: true
date: 2022-05-10T08:13:42.194Z
tags: docker, backup, sauvegarde, postgres, mysql, mariadb
editor: markdown
dateCreated: 2022-05-10T08:07:32.137Z
---

![logo](https://www.benlcollins.com/wp-content/uploads/2018/11/postgresql-logo.png =400x)
# PostgreSQL
## Récupération des informations
Afin de pouvoir sauvegarder proprement votre base de données (avec un dump), il vous faut les informations suivantes que vous pouvez récupérer directement depuis les variables d'envirionnement du conteneur :

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

![logo](https://upload.wikimedia.org/wikipedia/commons/thumb/6/68/Mariadb-seal-browntext.svg/2560px-Mariadb-seal-browntext.svg.png =400x)
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
## Sauvegarde
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