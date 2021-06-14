---
title: Protocole FTP
description: Qu’est-ce que c’est et à quoi ça sert ?
published: true
date: 2021-06-14T08:08:52.365Z
tags: 
editor: markdown
dateCreated: 2021-06-01T06:37:47.196Z
---

![App Insights: FTP Server | Apptopia](https://lh3.googleusercontent.com/uQD9VhVEe_oNMZ7ezJWOH0NR-fWwKjtNs0Vk-EG1jo6IAR31fR_StxS5zM8vlE6J2Bw)

# Présentation

**FTP (File Transfert Protocol)** est donc un protocole de **transfert de fichiers**.  
FTP obéit à un [modèle client-serveur](https://www.malekal.com/le-modele-ou-architecture-client-serveur/) :

-   Le serveur est un ordinateur sur lequel fonctionne un logiciel lui-même appelé serveur FTP rend publique une arborescence de fichiers similaire à un système de fichiers UNIX
-   Le client se connecte au serveur et établisse la connexion à travers des commandes FTP. Pour cela, on utilise **un logiciel client FTP** possédant une interface graphique ou en ligne de commande

L'administrateur du serveur FTP créé **des comptes FTP**.  
Ce sont des utilisateurs FTP avec un identifiant et mots de passe ou une clé publique de connexion.  
Chaque utilisateur a accès à une arborescence spécifique et des autorisations sur celle-ci.  
Une fois la connexion réussie, le client FTP accède à des dossiers autorisés par le compte FTP.  
Ce dernier peut **copier les fichiers en local ou distant :**

-   **Envoi (upload)** : lorsque le client envoie un fichiers vers le serveur FTP distant
-   **Réception (download)** : lorsque le client télécharge un fichier depuis le serveur FTP distant

Typiquement un client FTP se présente avec deux fenêtre, à gauche les dossiers et fichiers locaux, à droite, les dossiers et fichiers distants du serveur FTP.  
Vous pouvez faire glisser les fichiers de l'un à l'autre.

![Fonctionnement du FTP pour partager ses fichiers](https://www.malekal.com/wp-content/uploads/Tuto-fonctionnement-FTP.jpg)

## Le serveur FTP

Il existe plusieurs serveurs FTP sur Windows comme [FileZilla Server](https://www.malekal.com/fonctionnement-du-ftp-pour-partager-ses-fichiers/) ou **TypSoft FTP**, Windows possède aussi un serveur FTP.  
Sur Linux, on trouve **VsFTPd ou ProFTPD** parmi les plus populaires.

Le fonctionnement d'un serveur FTP est toujours le même, grosso modo :

-   Vous installez le programme qui fait office de serveur FTP
-   On créé les comptes (utilisateur / mot de passe)
-   On teste la connexion (il faut autoriser les connexions sur [pare-feu](https://www.malekal.com/le-fonctionnement-dun-pare-feu-ou-firewall-sur-windows/) etc).

Le serveur FTP fournit des journaux de connexions pour visualiser la négociation de connexion et les utilisateurs connectés.

![Fonctionnement du FTP pour partager ses fichiers](https://www.malekal.com/wp-content/uploads/Fonctionnement-FTP-partage-fichiers-FileZilla-5.jpg)

## **Le client FTP**

Il existe aussi une multitude de clients FTP graphiques comme [**FileZilla**](https://www.malekal.com/filezilla-client-ftp-windows-ubuntu/), **WinScp**, etc.

Là aussi, le principe reste le même, vous créez vos comptes FTP sur le client et en double-cliquant sur ce dernier, vous pouvez vous connecter au serveur.  
On trouve alors l'arborescence local et l'arborescence de fichiers distants.  
On peut alors faire un glisser déposer pour lancer le transferts de fichiers.

![Le client FTP : fonctionnement du partage par FTP](https://www.malekal.com/wp-content/uploads/Fonctionnement-FTP-partage-fichiers-FileZilla-7.jpg)

Sur Windows, on peut aussi se connecter en FTP par un lecteur réseau :

[Créer et se connecter en FTP avec un lecteur réseau sur Windows](https://www.malekal.com/creer-et-se-connecter-en-ftp-avec-un-lecteur-reseau-sur-windows/)  
 

Mais il en existe aussi en ligne de commandes.  
Windows et Linux possède par défaut la commande FTP qui propose le strict minimum.  
Mais sur Linux, on trouve des clients FTP plus puissants comme [**ncftp**](https://www.malekal.com/fonctionnement-du-ftp-pour-partager-ses-fichiers/) **ou lftp**.

Enfin il est même possible de **se connecter à un serveur FTP avec un navigateur WEB**.

# **Fonctionnement**

## **L'établissement de la connexion FTP**

Contrairement au [protocole HTTP](https://www.malekal.com/le-protocole-http-hypertext-transfer-protocol-versions-et-fonctionnement/), FTP fonctionne avec deux ports TCP : un port de contrôle (**Control Channel**) et un port pour les données (**Data Channel**).

|     |     |     |
| --- | --- | --- |
| **Protocole** | **Port de contrôle** | **Port de données** |
| FTP | 21  | 20  |
| FTPS | 990 | 989 |

*Les ports par défaut du protocole FTP*

Le client FTP **connecte au port de contrôle** où les premiers **échanges et négociations entre le client FTP et le serveur FTP** s'effectuent.  
Cette communication se fait sur le principe de commande / réponse.  
**Le Control Channel** perdure pour passer **des commandes FTP**.  
Par exemple, les commandes MLSD et LIST listent le contenu du dossier courant, la commande CWD permet de changer de dossier, PWD indique l'emplacement du dossier courant.

**Le Data Channel** permet l'échange de données.  
Lorsque le client demande à recevoir ou envoyer un fichier, une connexion [TCP](https://www.malekal.com/le-protocole-tcp-ip-definition-architecture-et-couches/) se créé puis les données passent par celle-ci.

Toutefois, il existe deux modes de connexion : [**Le mode actif et le mode passif**](https://www.malekal.com/le-mode-actif-et-passif-en-ftp-les-differences-et-fonctionnement/).

## **Les principales commandes FTP**

Le client FTP utilise ces commandes FTP pour dialoguer avec le serveur FTP à travers le Control Channel.  
Il existe environ 70 commandes FTP différentes.

|     |     |
| --- | --- |
| **Principales commandes FTP** | **Description** |
| ABOR | Annuler un transfert de fichier actif |
| CDUP | Passez au répertoire parent |
| CWD | Changer de répertoire de travail |
| DELE | Supprimer un fichier |
| EPSV | Entrer en mode passif étendu |
| LIST | Renvoyer les informations d'un fichier ou d'un répertoire si spécifié, sinon les informations du répertoire de travail actuel sont renvoyées |
| MKD | Créer un répertoire |
| MLSD | Répertorier le contenu d'un répertoire si un répertoire est nommé |
| PASS | Envoyer le mot de passe de connexion |
| PASV | Entrer en mode passif |
| PORT | Spécifier une adresse et un port auxquels le serveur doit se connecter |
| PWD | Afficher le répertoire de travail. Renvoie le répertoire actuel de l'hôte. |
| REIN | Réinitialiser la connexion |
| REST | Redémarrez le transfert à partir du point spécifié |
| RETR | Récupérer une copie du fichier |
| RMD | Renommer un dossier |
| RMDA | Supprimer une arborescence de répertoires |
| RNFR | Renommer depuis |
| RNTO | Renommer vers |
| QUIT | Se déconnecter du serveur |
| SIZE | Renvoyer la taille d'un fichier |
| TYPE | Définir le mode de transfert (ASCII / Binaire) |
| USER | S'authentifier par un nom d'utilisateur |

## **Le mode actif et passif**

FTP fonctionne sur deux modes de communications : le mode actif et passif.

Pourquoi et comment fonctionnent ces modes de communication ?  
Et surtout quelles sont les différences et lequel choisir ?

Ce tutoriel vous dit tout :

[Le mode actif et passif en FTP : les différences, fonctionnement, lequel choisir ?](https://www.malekal.com/le-mode-actif-et-passif-en-ftp-les-differences-et-fonctionnement/)  
 

# **Les autres protocoles FTP**

## **SFTP**

Le **SFTP** repose sur une connexion **SSH** et est donc chiffrée et accessible avec un client tel que **WinSCP**, **MobaXterm**, **Termius** ou encore **FileZilla**.

Il suffit pour cela de se connecter avec le compte SSH sur votre port SSH lui même.

## **TFTP**

**FTP** (pour ***Trivial File Transfer Protocol***) signifie **protocole simplifié de transfert de fichiers**.  
En clair donc, c'est un FTP avec des commandes simplifiées.  
Notamment, il n'est pas possible de lister les fichiers.  
De plus, il fonctionne sur le port 69 en [UDP](https://www.malekal.com/protocole-tcp-udp-icmp-fonctionnement-et-differences/).

Un serveur TFTP est parfois inclut dans les [NAS](https://www.malekal.com/serveur-nas/).

## **FTPS : FTP Over TLS et attaque MiTM**

Par défaut, **le protocole FTP n'est pas chiffrée**, ainsi lors de la connexion les identifiants FTP (nom d'utilisateur / mot de passe) passent en claire sur le réseau.  
[Une attaque de type Man in the Middle](https://www.malekal.com/man-in-the-middle/) peut permettre la récupération de ces identifiants FTP ou dérouter vers un faux serveur FTP.

**Le FTP existe sous la forme chiffrée à travers FTP Over TLS ou FTPS.**  
Ce dernier vise à sécuriser les connexions FTP et palier à aux manquements de FTP.

[*Source d'origine de la procédure*](https://www.tech2tech.fr/vmware-comment-mettre-a-jour-esxi/) | *Rédacteur Malekal.com*