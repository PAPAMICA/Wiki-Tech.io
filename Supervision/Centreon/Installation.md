---
title: Installation
description: 
published: true
date: 2021-06-14T08:12:34.546Z
tags: 
editor: markdown
dateCreated: 2021-05-24T10:37:11.632Z
---

![Centreon | Supervision Informatique : Infrastuctures Hybrides, Cloud, AIOps](https://static.centreon.com/wp-content/uploads/2019/10/Centreon_Logo_RVB_HD_Cut.png?x55598)

# Téléchargement de l’ISO

Il faut d’abord commencer par télécharger l’ISO de Centreon.  
Les différentes versions sont disponibles à l’adresse suivante :

[https://download.centreon.com/](https://download.centreon.com/)

Lors de l’écriture de cette documentation, la dernière version en date était « centreon-3.4.6.el7.x86\_64 »  
C’est celle-ci qui sera utilisée

# Installation de l’ISO

Selon l’installation sur un serveur physique ou virtuel, il faudra soit créer un live CD soit utiliser directement l’iso pour effectuer l’installation.  
Lors du démarrage, choisir « installer CentOS 7 »

![](/install_centreon-1.png)

Puis cliquer sur entrer.

![](/install_centreon-2.png)

L’installation se lance

![](/install_centreon-3.png)

Une fois l’installation terminée les différentes options de configuration apparaissent   
Choisir la langue puis cliquer sur continuer

![](/images/install_centreon-4.png)

A ce stade il est normal qu’en haut à droite le clavier soit indiqué comme étant « US ». Il sera à modifier par la suite.  
Le menu de configuration apparaît

![](/images/install_centreon-5.png)

Cliquer sur « Centreon » et choisir le type d’installation que l’on souhaite effectuer et cliquer sur terminer :

![](/images/install_centreon-6.png)

-   Central with database : Installe Centreon (interface web + base de données) ainsi que l’ordonnanceur et le broker
-   Central without database : Installe Centreon (interface web uniquement) ainsi que l’ordonnanceur et le broker
-   Poller : Installe le serveur satellite (ordonnanceur et broker uniquement)
-   Database only : Installe le serveur de base de données (utilisé en complément avec l’option Central server without database)

Pour cet exemple, nous allons installer « Central with database »  
Cliquer sur le clavier pour choisir le type de clavier utilisé.  
Cliquer sur le « + » et ajouter Français puis cliquer sur le « - » pour supprimer Français variante puis cliquer sur terminer

![](/images/install_centreon-7.png)

Cliquer sur « Date et Heure » pour choisir le fuseau et cliquer sur terminer

![](/images/install_centreon-8.png)

Activer ou ajouter des serveurs NTP

![](/images/install_centreon-9.png)

Cliquer sur « destination de l’installation »  
Cette étape permet de choisir le type de partitionnement souhaité : 

-   Automatique
-   Manuel
-   Libérer de l’espace

Cliquer sur « je vais configurer le partitionnement » puis sur terminer

![](/images/install_centreon-10.png)

A l’aide du bouton « + » créer le partitionnement

![](/images/install_centreon-11.png)

![](/images/install_centreon-12.png)

Il est conseillé de suivre les [*prérequis de la documentation*](https://documentation-fr.centreon.com/docs/centreon/fr/2.8.x/installation/prerequisites.html#diskspace)  
Lorsque c’est fait cliquer sur terminer

![](/images/install_centreon-13.png)

Une fenêtre de confirmation des modifications va apparaître. Cliquer sur accepter les modifications pour valider le partitionnement

![](/images/install_centreon-14.png)

Cliquer sur Nom d’hôte et réseau

![](/images/install_centreon-15.png)

Configurer les paramètres réseau et le nom de l’hôte Puis cliquer sur terminer

![](/images/install_centreon-16.png)

Tous les paramètres étant définis, cliquer sur commencer l’installation en bas à droite

![](/images/install_centreon-17.png)

Pendant l’installation il sera demandé de renseigner le mot de passe de l’administrateur dont le login est **root**

![](/images/install_centreon-18.png)

Il sera également demandé de créer un utilisateur avec login + mot de passe

![](/images/install_centreon-19.png)

Cliquer sur terminer et attendre la fin de l’installation  
Lorsque l’installation est terminée, cliquer sur redémarrer

![](/images/install_centreon-20.png)

# Mise à jour du système d’exploitation

Afin de mettre à jour le système d’exploitation il faut se connecter au serveur Centreon par SSH.

![](/images/putty.png)

Pour cela, utiliser le logiciel PuTTY, disponible à cette adresse 

[https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)

Une fois connecté taper la commande suivante :

```plaintext
yum update
```

![](/images/update_centreon-1.png)

Valider en tapant « y » (yes) à chaque fois que nécessaire

![](/images/update_centreon-2.png)

Si ce n’est pas demandé après avoir effectué les mises à jour, il est recommandé de faire un redémarrage du serveur

```plaintext
reboot
```

# Configuration

## L’Assistant de configuration de l’interface graphique

Une fois l’installation du serveur Centreon terminée, il sera nécessaire de le configurer.  
Pour cela, ouvrir un navigateur web et se connecter à cette adresse : **http://«IP\_DU\_SERVEUR»/centreon**  
Lors de la toute premier connexion, l’assistant de configuration de Centreon s’affiche. Cliquer sur suivant

![](/images/config_centreon-1.png)

L’assistant vérifie la disponibilité des modules de Centreon  
Cliquer sur suivant

![](/images/config_centreon-2.png)

Cliquer sur suivant 

![](/images/config_centreon-3.png)

Cliquer sur suivant 

![](/images/config_centreon-4.png)

Configuration des informations concertant l’utilisateur administrateur.  
Cliquer sur suivant.

![](/images/config_centreon-5.png)

Configuration du serveur de base de données.  
Le serveur sur lequel Centreon est installé est défini comme étant le serveur sur lequel la base de données sera installée par défaut.  
Son utilisateur est **root** et **le mot de passe par défaut est vide.**  
**Le laisser vide sans rien écrire.**  
Si l’on souhaite utiliser un serveur de base de données déporté, il faudra dans ce cas indiquer les informations de connexion au serveur (adresse, utilisateur mot de passe, etc.)

Cliquer sur suivant

![](/images/config_centreon-6.png)

_Attention, si le message d’erreur suivant apparaît :_

***Add innodb\_file\_per\_table=1 in my.cnf file under the \[mysqld\] section and restart MySQL Server.*** 

Faire cette manipulation pour le résoudre :   
Se connecter en SSH avec l’utilisateur root sur le serveur.

Editer le fichier /etc/my.cnf

```plaintext
vi /etc/my.cnf
```

Ajouter cette ligne au fichier

```plaintext
[mysqld]
innodb_file_per_table=1
```

Redémarrez le service mysql

```plaintext
service mysql restart
```

Cliquez sur suivant.  
L’assistant configure les bases de données.

![](/images/config_centreon-7.png)

Une fenêtre d’information concernant les dernières nouveautés de Centreon va apparaître.  
Cliquer sur terminer

![](/images/config_centreon-8.png)

Il est maintenant possible de se connecter 

![](/images/login_centreon-1.png)

## Configurations de base

Pour passer l’interface graphique en français il faut se connecter avec l’utilisateur root au serveur par SSH.  
Dans un premier temps, installer le paquet de traduction en français à l’aide de la commande suivante :

```plaintext
yum -y install centreon-lang-fr_FR
```

![](/images/config_centreon-9.png)

Dans un deuxième temps, se connecter à l’interface graphique et cliquer sur Administration.  
Dans la partie de gauche cliquer sur « Parameters » puis sur « My Account 

![](/images/config_centreon-10.png)

Dans le champ des langues remplacer « en\_US » par « fr\_FR.UTF-8 ». Une fois la page rafraichie, l’interface sera passée en français.  
Cliquer sur sauvegarder.

![](/images/config_centreon-11.png)

## Démarrer la supervision

-   Générer les fichiers de configuration : Génère les fichiers de configuration de l’ordonnanceur dans un répertoire temporaire. Cette configuration est générée à partir des objets configurés via l’interface web
-   Lancer le débogage du moteur de supervision (-v) : Permet à l’ordonnanceur de vérifier la configuration générée
-   Déplacer les fichiers générés : Déplace les fichiers de configuration du répertoire temporaire vers le répertoire de l’ordonnanceur
-   Redémarrer l’ordonnanceur : Redémarre l’ordonnanceur afin d’appliquer les nouveaux fichiers de configuration
-   Commande exécutée post-génération : Exécute la commande post-génération paramétrée au niveau de la configuration de l’ordonnanceur

### Première étape

Pour démarrer la supervision il faut se rendre sur l‘interface graphique.  
Dans le menu Configuration puis « collecteurs », tout laisser par défaut et cliquer sur exporter.

![](/images/config_centreon-12.png)

Une nouvelle page apparait.

![](/images/config_centreon-13.png)

Dans la partie « Collecteurs », sélectionner le serveur 

![](/images/config_centreon-14.png)

Vérifier que les cases « Générer les fichiers de configuration » et « Lancer le débogage du moteur de supervision (-v) » soient bien cochées. Vérifier que les cases « Déplacer les fichiers générés » ainsi que « Redémarrer l’ordonnanceur » soient bien décochées.  
Cliquer à nouveau sur « Exporter ».  
Si tout s’est bien passé, les messages « ok » apparaissent. Dans le cas contraire il faudra corriger les erreurs

![](/images/config_centreon-15.png)

### Deuxième étape

Décochez les cases « Générer les fichiers de configuration » et « Lancer le débogage du moteur de supervision (-v) »  
Cochez les cases « Déplacer les fichiers générés » ainsi que « Redémarrer l’ordonnanceur »

![](/images/config_centreon-16.png)

Cliquez sur « Exporter » à nouveau  
Si tout s’est bien passé, les messages « ok » apparaissent à nouveau. Dans le cas contraire il faudra corriger les erreurs

![](/images/config_centreon-17.png)

### Troisième étape

Se connecter avec l’utilisateur root au serveur par SSH.  
Démarrer le composant Centreon Broker avec la commande suivante :

```plaintext
service cbd start
```

Démarrer Centreon Engine avec la commande suivante :

```plaintext
service centengine start
```

Démarrer centcore avec la commande suivante :

```plaintext
service centcore start
```

La supervision est maintenant opérationnelle.

![](/images/config_centreon-18.png)

***Attention**, la supervision est active mais pour l’instant _RIEN n’est supervisé_. En effet, il reste à configurer les sondes selon ce que l’on souhaite superviser. Il est également possible d’installer des add-on et des plugins qu’il faudra les configurer également.*

# Découverte de l’interface Web

L’interface graphique de Centreon par l’intermédiaire de l’adresse **http://«IP\_DU\_SERVEUR»/centreo**n propose plusieurs menus avec des fonctions bien précises. Chacun de ces menus possède des sous sections.

![](/images/interface_centreon-1.png)

-   Accueil : Premier écran d’accueil après connexion sur l’interface graphique. Résume l’état général de la supervision.
-   Supervision : Regroupe l’état de tous les éléments supervisés en temps réel et en différé. 
-   Rapports : Permet de visualiser à l’aide de diagrammes et de graphiques l’évolution de la supervision sur une période ciblée.
-   Configuration : Configure l’ensemble des éléments supervisés et l’infrastructure de supervision.
-   Administration : Configure l’interface web de Centreon et permet de visualiser l’état général des serveurs.
-   Vues : Visualiser et configurer les graphiques de performances pour chaque élément du système d’informations.

# Pack de Plugins – Facilitation de la supervision

Centreon est un excellent outil de supervision qui peut être configuré manuellement pour correspondre exactement aux différents besoins de l’entreprise. Toutefois, il existe des packs de plugins contenant des modèles de configuration.  
Ces différents packs réduisent le temps nécessaire pour installer et superviser la plupart des services de l’entreprise.  
Pour fonctionner, les composants techniques Centreon License Manager et Centreon Plugin Pack Manager sont nécessaires.  
L’installation de ces deux paquets est très simple.  
Il suffit de se connecter sur le serveur Centreon avec l’utilisateur root en SSH et de taper la commande suivante.

```plaintext
yum install centreon-pp-manager
```

Cette commande téléchargera Centreon Plugin Pack Manager et également Centreon License Manager en tant que dépendance.

Une fois ces deux paquets téléchargés il faudra les installer et les activer dans l’interface graphique de Centreon  
Section Administration puis Extensions et enfin Modules.

![](/images/plugins_centreon-1.png)

Le premier module à installer et activer sera Centreon License Manager

![](/images/plugins_centreon-2.png)

Puis il faudra installer et activer Centreon Plugin Pack Manager

![](/images/plugins_centreon-3.png)

Il est désormais possible de se rendre dans la section « Configuration » puis « Plugin packs » pour trouver les 6 plugins gratuits proposés. Si on s’inscrit sur le site de Centreon, 5 autres plugins packs deviennent gratuits, les autres étant payant.

Il existe plus de 150 plugins packs disponibles dans l’offre IMP.

![](/images/plugins_centreon-4.png)