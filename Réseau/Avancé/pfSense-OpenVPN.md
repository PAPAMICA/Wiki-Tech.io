---
title: pfSense-OpenVPN
description: Configurer un serveur OpenVPN sur pfSense.
published: true
date: 2021-06-14T08:10:06.330Z
tags: 
editor: markdown
dateCreated: 2021-05-24T10:36:30.079Z
---

![About OpenVPN | OpenVPN](https://openvpn.net/wp-content/uploads/2018/06/about_text_logo.png)

# OpenVPN sur un pfSense

Configurer un serveur VPN sur le routeur pfSense vous permettra d’accéder à l’ensemble de votre réseau à distance de façon sécurisée.

Pour la sécurité nous allons utiliser des certificats autosignés.

## Les certificats :

#### Nous allons avoir besoin de plusieurs certificats :

-   un certificat pour le VPN
-   un certificat pour le serveur
-   un certificat pour les utilisateurs

#### Commençons par le certificat pour le VPN :

-   Rendez vous dans la partie : **Système > Gestionnaire de certificats**.
-   Restez dans l’onglet “**ACs**” et cliquez sur **Ajouter**.
-   Remplissez les informations suivantes (en remplaçant par la localité de votre serveur et votre adresse mail) :

![](https://i0.wp.com/labo-tech.fr/wp-content/uploads/2018/03/certificat-OpenVPN-920x727.png?resize=920%2C727)

-   Une fois enregistré, vous devriez avoir ceci :

![](https://i1.wp.com/labo-tech.fr/wp-content/uploads/2018/03/resultat-ca-openvpn-920x186.png?resize=920%2C186)

#### Continuons avec le certificat pour le serveur :

-   Restez dans le **Gestionnaire de certificats** mais rendez vous dans l’onglet **Certificats.**
-   Remplissez les informations suivantes (en remplaçant l’ip par celle de votre routeur pfSense) :

![](https://i0.wp.com/labo-tech.fr/wp-content/uploads/2018/03/certificat-pfSense-920x1027.png?resize=920%2C1027)

#### Et finissons par le certificat pour les utilisateurs :

-   Rendez vous dans la partie : **Système > Gestionnaire d’usagers.**
-   Nous allons ajouter une utilisateur pour le VPN, cliquez donc sur **Ajouter**.
-   Remplissez les informations suivantes (en remplaçant votre utilisateur et son mot de passe) :

![](https://i1.wp.com/labo-tech.fr/wp-content/uploads/2018/03/certificat-utilisateur-920x1052.png?resize=920%2C1052)

## Installation et configuration d’OpenVPN

Une fois les certificats créés, nous pouvons attaquer l’installation et la configuration sur serveur OpenVPN.

#### Pour cela nous allons avoir besoin d’un paquet qui n’est pas installé par défaut :

-   Rendez vous dans la partie : **Système > Gestionnaire de paquets** puis dans l’onglet **Paquets disponibles.**
-   Il nous faut le paquet : **openvpn-client-export**, descendez jusqu’à le trouver et cliquez sur **+Install** :

![](https://i1.wp.com/labo-tech.fr/wp-content/uploads/2018/03/paquet-openvpnclientexport-920x76.png?resize=920%2C76)

-   Une fois installé, vous devriez le retrouver dans l’onglet **Paquets installés**.

#### Configuration du serveur OpenVPN :

-   Rendez vous dans la partie : **VPN > OpenVPN** puis dans l’onglet “**Assistants**“.
-   Étape 1 : sélectionnez “**Local User Access**“.
-   Étape 5 : sélectionnez le **certificat VPN** précédemment créé.
-   Etape 7 : selectionnez le **certificat Utilisateur** précédemment créé.
-   Etape 9 : rentrez les informations suivantes :

![](https://i1.wp.com/labo-tech.fr/wp-content/uploads/2018/03/serveur-openvpn_conf1-920x3140.png?resize=920%2C3140)

-   Etape 10 : Cochez “**Firewall rule**” et “**OpenVPN rule**“.
-   Et voilà ! Le serveur est configuré !

## Connexion au VPN

Afin de pouvoir vous connectez à ce VPN, il va falloir exporter la configuration et l’importer dans votre client que ça soit sur Windows, MacOS, Linux, Android ou encore iOS.

#### Exportation de la configuration :

-   Rendez vous dans la partie : **VPN > OpenVPN** puis dans l’onglet “**Client Export**“.
-   Laissez la configuration de base et dirigez vous vers le bas de la page.
-   Il ne vous reste plus qu’à choisir votre plateforme :

![](https://i1.wp.com/labo-tech.fr/wp-content/uploads/2018/03/export-conf-920x361.png?resize=920%2C361)

Pour Windows, je vous conseille directement le Windows Installer qui, même si vous avez déjà le client, placera les fichiers de configuration au bon endroit.

#### Se connecter au VPN :

1.  Une fois installé, vous allez trouver une nouveau logiciel : **OpenVPN GUI.**
2.  Lancez le et sélectionnez, dans la barres des tâches, le serveur VPN que vous venez de configurer.
3.  Rentrez les identifiants de l’utilisateur créé précédemment dans pfSense.

1.  Vous devriez voir ceci :

![](https://i0.wp.com/labo-tech.fr/wp-content/uploads/2018/03/connecte-.png?resize=370%2C125)

Vous voilà connecté au VPN !