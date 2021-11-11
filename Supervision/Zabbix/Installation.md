---
title: Zabbix - Installation
description: Toute la procédure pour mettre en place un serveur Zabbix
published: true
date: 2021-10-05T18:59:53.327Z
tags: supervision, zabbix
editor: markdown
dateCreated: 2021-10-05T15:27:25.169Z
---

![image_2021-10-05_122334.png](/images/zabbix/installation/image_2021-10-05_122334.png)

# Introduction
Nous allons voir deux méthodes pour l'installation de Zabbix : la première, plus classique, directement sur Debian 11 et la seconde aue je vous conseil avec Docker. La suite des outils lié avec Zabbix se reposera sur l'installation avec la seconde option.
# Debian
## Installation 
L'installation se fera sur une machine Debian 11 avec une base de données **MySQL** et **Apache**.

### Mettre à jour Debian
```bash
sudo apt update && sudo apt upgrade
```

### Ajoutez le dépot Zabbix
```bash
wget https://repo.zabbix.com/zabbix/5.4/debian/pool/main/z/zabbix-release/zabbix-release_5.4-1+debian11_all.deb
sudo dpkg -i zabbix-release_5.4-1+debian11_all.deb
sudo apt update
```

### Installez tous les paquets necessaires pour le serveur Zabbix
```bash
sudo apt install zabbix-server-mysql zabbix-frontend-php zabbix-apache-conf zabbix-sql-scripts zabbix-agent2 mariadb-server
```

### Créez la base de données
```bash
sudo mysql -u root -p
mysql> create database zabbix character set utf8 collate utf8_bin;
mysql> create user zabbix@localhost identified by 'password';
mysql> grant all privileges on zabbix.* to zabbix@localhost;
mysql> quit;
```
> Pensez à changer le mot de passe `password` !
{.is-warning}

### Importez le schéma et les données initiales
Vous serez invité à entrer votre mot de passe nouvellement créé.
```bash
sudo zcat /usr/share/doc/zabbix-sql-scripts/mysql/create.sql.gz | mysql -uzabbix -p zabbix
```

### Configurer la base de données pour le serveur Zabbix
Modifier le fichier `/etc/zabbix/zabbix_server.conf` :
```bash
DBPassword=password
```
> Pensez à changer le mot de passe `password` !
{.is-warning}

### Démarrer les processus du serveur et de l'agent Zabbix
Démarrez les processus du serveur et de l'agent Zabbix et faites-les démarrer au démarrage du système.
```bash
sudo systemctl restart zabbix-server zabbix-agent2 apache2
sudo systemctl enable zabbix-server zabbix-agent2 apache2
```
> Vorte serveur Zabbix est installé ! Il est accessible depuis : `http://ip_or_name/zabbix`
{.is-success}


## Configuration
### Connectez vous sur l'interface web de votre serveur Zabbix
![image_2021-10-05_194738.png](/images/zabbix/installation/image_2021-10-05_194738.png)
### Vérifiez que tous les pré-requis sont bien "OK"
![image_2021-10-05_194837.png](/images/zabbix/installation/image_2021-10-05_194837.png)
### Configurez la connexion à la base de données
![image_2021-10-05_194950.png](/images/zabbix/installation/image_2021-10-05_194950.png)
### Nommez votre serveur Zabbix
![image_2021-10-05_195624.png](/images/zabbix/installation/image_2021-10-05_195624.png)
### Selectionnez le bon fuseau horaire
![image_2021-10-05_195744.png](/images/zabbix/installation/image_2021-10-05_195744.png)
### Vérifier que les informations sont correctes
![image_2021-10-05_195836.png](/images/zabbix/installation/image_2021-10-05_195836.png)
### Terminez l'installation en cliquant sur "Finish"
![image_2021-10-05_195924.png](/images/zabbix/installation/image_2021-10-05_195924.png)
### Connectez vous avec l'identifiant `Admin` et le mot de passe `zabbix`
![image_2021-10-05_200200.png](/images/zabbix/installation/image_2021-10-05_200200.png =500x)
> Pensez à changer le mot de passe du compte Admin !
{.is-warning}

> Toutes mes félicitations, vous pouvez maintenant superviser votre premier serveur !
{.is-success}




# Docker
Vous pouvez déployer rapidement et simplement un serveur Zabbix et sa base de données avec ce docker-compose :
```yaml
version: '2'
services:
  postgres-server:    # The Postgres Database Service
    image: postgres:12.2
    restart: always
    container_name: zabbix-db
    environment:   # Username, password and database name variables
      POSTGRES_USER: zabbix
      POSTGRES_PASSWORD: $DB_PASSWORD
      POSTGRES_DB: zabbix
      PG_DATA: /var/lib/postgresql/data/pgdata #data storage
    volumes:
      - /apps/zabbix/db:/var/lib/postgresql/data
      
    # Facultatif  
    networks:
      - proxy
    logging:
      driver: loki
      options:
        loki-url: "$URL_LOKI"
        loki-external-labels: service={{.Name}}

  zabbix-server:     # The main Zabbix Server Software Service
    image: zabbix/zabbix-server-pgsql:ubuntu-5.4-latest
    restart: always
    container_name: zabbix
    environment:   # The Postgres database value variable
      POSTGRES_USER: zabbix
      POSTGRES_PASSWORD: $DB_PASSWORD
      POSTGRES_DB: zabbix
      ZBX_HISTORYSTORAGETYPES: log,text #Zabbix configuration variables
      ZBX_DEBUGLEVEL: 1
      ZBX_HOUSEKEEPINGFREQUENCY: 1
      ZBX_MAXHOUSEKEEPERDELETE: 5000
      ZBX_STARTVMWARECOLLECTORS: 10
      ZBX_VMWAREFREQUENCY: 30
      ZBX_VMWAREPERFFREQUENCY: 30
      ZBX_VMWARECACHESIZE: 16M
      ZBX_VMWARETIMEOUT: 10
    depends_on:
      - postgres-server
    ports:
      - 10051:10051
    volumes:  # Volumes for scripts and related files you can add
      - /apps/zabbix/alertscripts:/usr/lib/zabbix/alertscripts
    
    # Facultatif
    networks:
      - proxy
    logging:
      driver: loki
      options:
        loki-url: "$URL_LOKI"
        loki-external-labels: service={{.Name}}
      
  zabbix-web:    # The main Zabbix web UI or interface
    image: zabbix/zabbix-web-nginx-pgsql:ubuntu-5.2-latest
    restart: always
    container_name: zabbix-web
    environment:  # Postgre database variables
      POSTGRES_USER: zabbix
      POSTGRES_PASSWORD: $DB_PASSWORD
      POSTGRES_DB: zabbix
      ZBX_SERVER_HOST: zabbix-server  # Zabbix related and Php variables
      ZBX_POSTMAXSIZE: 64M
      PHP_TZ: "Europe/Paris"
      ZBX_MAXEXECUTIONTIME: 500
    depends_on:
      - postgres-server
      - zabbix-server
#    ports:    # Port where Zabbix UI is available
#      - 8090:8080

		# Facultatif
    networks:
      - proxy
    logging:
      driver: loki
      options:
        loki-url: "$URL_LOKIh"
        loki-external-labels: service={{.Name}}
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.zabbix.entrypoints=http"
      - "traefik.http.routers.zabbix.rule=Host(`$URL`)"
      - "traefik.http.middlewares.zabbix-https-redirect.redirectscheme.scheme=https"
      - "traefik.http.routers.zabbix.middlewares=zabbix-https-redirect"
      - "traefik.http.routers.zabbix-secure.entrypoints=https"
      - "traefik.http.routers.zabbix-secure.rule=Host(`$URL`)"
      - "traefik.http.routers.zabbix-secure.tls=true"
      - "traefik.http.routers.zabbix-secure.tls.certresolver=http"
      - "traefik.http.services.zabbix-secure.loadbalancer.server.port=8080"
      - "traefik.docker.network=proxy"
      

networks:
  proxy:
    external:
      name: proxy
```
> Pensez à changer dans le docker-compose ou à définir les variables suivantes : **DB_PASSWORD**, **URL_LOKI** et **URL** en fonction de votre installation.
{.is-warning}

> Votre serveur Zabbix est accessible via l'URL que vous avez configuré dans le docker-compose !
{.is-success}

> L'identifiant par défaut est `Admin` et le mot de passe est `zabbix`.
{.is-info}

