---
title: Notifications e-mails
description: Configuration des notifications par e-mails
published: true
date: 2021-06-14T08:13:58.136Z
tags: 
editor: markdown
dateCreated: 2021-05-24T10:37:13.809Z
---

![Centreon | Supervision Informatique : Infrastuctures Hybrides, Cloud, AIOps](https://static.centreon.com/wp-content/uploads/2019/10/Centreon_Logo_RVB_HD_Cut.png?x55598)

# Configuration des notifications par email

## Configuration des comptes utilisateur

Pour permettre la notification des différentes alertes créées dans centreon il va falloir effectuer une configuration dans le profil du ou des utilisateurs qui les recevront.  
Cliquer sur « Configuration » puis sur « Users », sur « Contacts » et en fin sur « Users ».  
Choisir l’utilisateur concerné et effectuer les modifications suivantes dans les section « notification », « hosts » et « services » :

-   Enable notifications : yes
-   Host /Service Notification Options : cocher les cases pour lesquelles on veut recevoir des notifications
-   Host / Service Notification Period : 24x7 pour une notification 24h/24 et 7j/7
-   Host Notification Commands : host-notify-by-email
-   Service Notification Commands : service-notify-by-email

![](/images/mail_centreon-1.png)

Cliquer sur « save »  
Il sera également nécessaire d’effectuer la configuration suivante sur les Templates de service et Template d’hôtes.

![](/images/mail_centreon-2.png)

Cliquer sur « Save »

## Configuration du postfix

Attention SI postfix n’est pas installé sur le serveur il faudra l’installer au préalable 

```plaintext
yum install postfix -y
```

Afin de permettre les différentes notifications par email, il va falloir modifier le fichier */etc/postfix/mail.cf*

```plaintext
vi /etc/postfix/main.cf
```

Une fois dans le fichier, il va falloir modifier les lignes suivantes : 

-   #myhostname = host.domain.tld -> retirer le # et à la place de « host.domain.tld » mettre le nom du serveur centreon
-   #myorigin = $myhostname -> retirer le #
-   #relayhost = \[an.ip.add.ress\] -> retirer le # et mettre l’adresse IP du relay SMTP

Sauvegarder le fichier puis relancer le service postfix à l’aide de la commande suivante

```plaintext
/etc/init.d/postfix restart
```

Les notifications par e-mail sont maintenant activées.

## BONUS : Modification du visuel des notifications par email

Les notifications par email qui sont envoyées par centreon sont relativement sommaires et pas forcément très lisible cependant il est possible de les modifier.  
A titre d’exemple, cette procédure détaillera comment passer de ce type de notification (exemple trouvé sur Internet)

![](/images/mail_centreon-3.png)

A ce type ci 

![](/images/mail_centreon-4.png)

Merci à [**Shini31**](https://github.com/Shini31) pour le partage de son travail.

### Cloner le dépot git depuis git-hub

Si la commande GIT n’est pas installée il faudra avant tout effectuer cette commande 

```plaintext
yum install git
```

Ensuite, il va falloir cloner le dépôt git permettant de modifier les notifications.

```plaintext
cd /tmp
Mkdir notif
cd notif/
git clone https://github.com/Shini31/centreon-notifications.git
 Cloning into 'centreon-notifications'...
 remote: Counting objects: 101, done.
 remote: Total 101 (delta 0), reused 0 (delta 0), pack-reused 101
 Receiving objects: 100% (101/101), 107.24 KiB | 0 bytes/s, done.
 Resolving deltas: 100% (40/40), done.
```

Se déplacer dans le dossier « centreon-notifications » qui vient d’être téléchargé depuis GitHub

```plaintext
cd centreon-notifications/mail/
```

A l’aide de la commande « ls -l » nous pouvons vérifier la présence des fichiers suivant 

-   host-email.php
-   host\_email.png
-   service-email.php
-   service\_email.png

```plaintext
ls -l
total 68
-rw-r--r-- 1 root root  2757 31 août  16:20 host-email.php
-rw-r--r-- 1 root root 22334 31 août  16:20 host_email.png
-rw-r--r-- 1 root root  5178 31 août  16:20 service-email.php
-rw-r--r-- 1 root root 32747 31 août  16:20 service_email.png
```

### Configuration des fichiers

Déplacer les fichiers « host-email.php » et « service-email.php » dans */usr/lib/nagios/plugins/*

```plaintext
mv host-email.php /usr/lib/nagios/plugins/
mv service-email.php /usr/lib/nagios/plugins/
```

Se placer dans le même répertoire

```plaintext
cd /usr/lib/nagios/plugins/
```

Effectuer les commandes suivantes :

```plaintext
chown centreon:centreon host-email.php
chown centreon:centreon service-email.php
chmod 775 host-email.php
chmod 775 service-email.php
```

Il va falloir modifier les deux fichiers.

```plaintext
vi host-email.php
```

Rechercher à l’aide de « / » les lignes contenant :

-   *$subjects* puis supprimer \[CENTREON\]. Cela permettra une meilleure visibilité dans la lecture des emails.
-   *$url* mettre l’adresse web du Centreon en FQDN
-   $from mettre l’adresse mail que centreon utilisera pour envoyer des mails (ex : centreon-engine@server.localdomain)

Sauvegarder avec « :wq »

Ensuite il faudra faire exactement les mêmes modifications dans le fichier « service-email.php »

```plaintext
vi service-email.php
```

Sauvegarder avec « :wq »

### Configuration des notifications dans centreon

Se connecter à l’interface web du serveur centreon puis cliquer sur « Configuration » puis « Commands » puis sur « Notifications »  
Sur cette page, les différentes notifications préconfigurées de Centreon sont disponibles.

Avant de les modifier il est conseillé de dupliquer les commandes « host-notify-by-email » et « service-notify-by-email » pour les sauvegarder. En cas de problème on pourra toujours récupérer les notifications d’origine.

![](/images/mail_centreon-5.png)

Une fois les deux sauvegardes sont effectuées, il va falloir modifier « host-notify-by-email » et « service-notify-by-email »  
Pour cela cliquer dessus et supprimer l’intégralité de la case « command line » pour y ajouter respectivement : 

-   host-notify-by-email

```plaintext
$USER1$/host-email.php "$NOTIFICATIONTYPE$" "$HOSTNAME$" "$HOSTALIAS$" "$HOSTSTATE$" "$HOSTADDRESS$" "$HOSTOUTPUT$" "$LONGDATETIME$" "$SERVICEDESC$" "$SERVICESTATE$" "$CONTACTEMAIL$" "$TOTALHOSTSUP$" "$TOTALHOSTSDOWN$" "$HOSTACKAUTHOR$" "$HOSTACKCOMMENT$"
```

Cliquer sur « save »

-   service-notify-by-email

```plaintext
$USER1$/service-email.php "$NOTIFICATIONTYPE$" "$HOSTNAME$" "$HOSTALIAS$" "$HOSTSTATE$" "$HOSTADDRESS$" "$SERVICEOUTPUT$" "$LONGDATETIME$" "$SERVICEDESC$" "$SERVICESTATE$" "$CONTACTEMAIL$" "$SERVICEDURATIONSEC$" "$SERVICEEXECUTIONTIME$" "$TOTALSERVICESWARNING$" "$TOTALSERVICESCRITICAL$" "$TOTALSERVICESUNKNOWN$" "$LASTSERVICEOK$" "$LASTSERVICEWARNING$" "$SERVICENOTIFICATIONNUMBER$" "$SERVICEACKAUTHOR$" "$SERVICEACKCOMMENT$"
```

Cliquer sur « save »

Une fois les deux notifications modifiées, il ne reste plus qu’à exporter la configuration du poller comme indiqué dans le prochain chapitre « **DEPLOYER LA CONFIGURATION** »