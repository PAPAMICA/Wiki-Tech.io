---
title: Zabbix - Installation
description: Toute la procédure pour mettre en place un serveur Zabbix
published: true
date: 2021-10-05T15:27:25.169Z
tags: supervision, zabbix
editor: markdown
dateCreated: 2021-10-05T15:27:25.169Z
---

![image_2021-10-05_122334.png](/images/zabbix/installation/image_2021-10-05_122334.png)

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



# Docker
