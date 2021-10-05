---
title: Zabbix - Installation
description: Toute la procédure pour mettre en place un serveur Zabbix
published: true
date: 2021-10-05T18:46:35.372Z
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
