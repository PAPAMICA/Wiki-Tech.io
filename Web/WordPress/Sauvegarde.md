---
title: Sauvegarder son site WordPress
description: Les différentes méthodes de sauvegardes de votre WordPress
published: true
date: 2021-06-16T08:30:16.060Z
tags: wordpress, backup, sauvegarde
editor: markdown
dateCreated: 2021-06-15T14:46:16.525Z
---

![Fichier:WordPress logo.svg — Wikipédia](https://upload.wikimedia.org/wikipedia/commons/thumb/2/20/WordPress_logo.svg/1200px-WordPress_logo.svg.png)

# Présentation

Comme pour tous systèmes, la sauvegarde est essentielle en cas d'erreurs, de fausses manipulations ou d’incendies de DataCenter 🙃

Il existe plusieurs méthodes et procédures pour faire la sauvegarde votre site WordPress. Je vais aborder ici mes habitudes de sauvegardes en commençant par mon planning :

-   Une sauvegarde par jour ou par semaine avec l'extension UpdraftPlus
-   Une sauvegarde mensuelle des fichiers et de la base de données manuellement

# UpdraftPlus

## Présentation

[UpdraftPlus est un plugin WordPress gratuit](https://fr.wordpress.org/plugins/updraftplus/), simple à utiliser et fiable pour créer des sauvegardes de votre site ou de votre blog. Il permet également de restaurer une sauvegarde et de migrer ou cloner un site WordPress.

## Installation

Comme pour toutes les extensions, le processus est assez simple : 

![](/images/wordpress/sauvegarde/wordpress_sauvegarde_1.png)

C'est par ici que ça se passe : `Extensions` > `Ajouter` > `Chercher` > `UpdraftPlus` > `Installer Maintenant` > `Activer`

## Configuration des sauvegardes

Rendez-vous dans les paramètres de UpdraftPlus pour configurer les sauvegardes. 

1 - Commencez par régler la régularité et la rétention des sauvegardes automatiques (4)

2 - Choisissez votre stockage externalisé pour la sauvegarde (5) (Dans mon cas : sur mon stockage Swiss Backup chez Infomaniak)

3 - Configurez le stockage correctement et testez le (6)

![](/images/wordpress/sauvegarde/wordpress_sauvegarde_2.png)

4 - Laissez tout coché pour les éléments à inclure dans la sauvegarde

5 - Pensez à cocher la case `Courriel` afin d'être averti par mail de l'état des sauvegardes

![](/images/wordpress/sauvegarde/wordpress_sauvegarde_3.png)

6 - Vérifier que tout fonctionne en lançant une sauvegarde depuis l'onglet “`Sauvegarder/restaurer`”

![](/images/wordpress/sauvegarde/wordpress_sauvegarde_4.png)

> N'hésitez pas à vérifier de temps en temps que les sauvegardes se font correctement, c'est important de garder un œil dessus !
{.is-info}

# Sauvegarde de l'arborescence

J'effectue cette sauvegarde en général une fois par mois, cela permet d'avoir une sauvegarde complète de tous les fichiers du site dans une archive stockée chez soi, dans mon cas sur un de mes NAS. Le but étant ici de sauvegarder tous les fichiers dans `/var/www/html`.

## Installé avec Docker

Si vous avez installé WordPress avec Docker à l'aide de cette procédure, vous devriez localiser les fichiers du site au chemin que vous avez rentré dans votre Docker-Compose ici :

`**/apps/wordpress/html**:/var/www/html`

### Sauvegarde manuelle

Connectez vous à votre serveur en SSH et rendez vous dans le dossier précédemment cité et nous allons faire une sauvegarde en créant une archive :

```bash
# Se connecter en SSH et se rendre dans le dossier :
ssh <USER>@<IP> -p <PORT>
cd /apps/wordpress

# Créer une archive du dossier html :
tar -cvf backup-site-date.tar html

# Copier la sauvegarde sur votre poste :
scp -P <PORT> <USER>@<IP>:/apps/wordpress/backup-site-date.tar /home/<USER>/Téléchargements
```

Il ne vous reste plus qu'a stocker cette archive là où vous le souhaitez.

## Hébergé chez un fournisseur

Normalement votre hébergeur vous fournit un accès FTP ou SFTP au stockage de votre site.

Connectez vous avec les informations de connexion sur FileZilla pour créer une archive et la télécharger. 

# Sauvegarde Base de données

## Installé avec Docker

1 - Connectez vous en SSH à votre serveur

2 - Installez le client mariadb si vous ne l'avez pas : 

```bash
apt install mariadb-client
```

3 - Lancez la sauvegarde de la base de données avec la commande :

```bash
docker exec <CONTAINER-NAME> /usr/bin/mysqldump -u root --password=<PASSWORD-ROOT> <DATABASE-NAME> > backup.sql
```

Vous devriez trouver dans votre dossier actuel un nouveau fichier `backup.sql` contenant le dump de votre base de données. 

Pour restaurer la base de données :

```bash
cat backup.sql | docker exec -i <CONTAINER-NAME> /usr/bin/mysql -u root --password=<PASSWORD-ROOT> <DATABASE-NAME>
```

## Hébergé chez un fournisseur

Si vous avez un bon hébergeur, ce dernier vous donnera la possibilité de télécharger l'une des précédentes sauvegardes de la base de données de votre site directement depuis le panel d'administration :

### Infomaniak

![](/images/wordpress/sauvegarde/wordpress_sauvegarde_5.png)

### OVH

![](/images/wordpress/sauvegarde/wordpress_sauvegarde_6.png)