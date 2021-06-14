---
title: Les hôtes
description: Les hôtes dans Centreon
published: true
date: 2021-06-14T08:13:48.263Z
tags: 
editor: markdown
dateCreated: 2021-05-24T10:37:09.429Z
---

![Centreon | Supervision Informatique : Infrastuctures Hybrides, Cloud, AIOps](https://static.centreon.com/wp-content/uploads/2019/10/Centreon_Logo_RVB_HD_Cut.png?x55598)

# Les Hôtes

## Qu’est-ce qu’un hôte

Par définition, un hôte représente toute entité correspondant à une ressource du système d’information de l’entreprise et possédant une adresse IP.  
A titre d’exemple, nous pouvons citer : 

-   Les serveurs
-   Les ordinateurs
-   Les NAS
-   Une base de données
-   Etc.

## Ajout d’un hôte

Pour ajouter un hôte il faut se connecter à l’interface graphique de Centreon avec un compte administrateur ou un compte qui possède des droits suffisant pour le faire.  
Cliquer sur la partie « Configuration » puis « Hôtes ».

![](/images/hôtes_centreon-1.png)

Cliquer sur « Ajouter » 

![](/images/hôtes_centreon-2.png)

Une nouvelle fenêtre apparait.

![](/images/hôtes_centreon-3.png)

### Les onglets

#### Configuration de l’hôte

-   Nom : indique le nom d’hôte qui sera utilisé par le moteur de supervision.
-   Alias : indique l’alias de l’hôte.
-   Adresse IP / DNS : Adresse IP ou nom DNS de l’hôte (FQDN).
    -   Le bouton Résoudre permet de tester la résolution du nom de domaine en interrogeant le serveur DNS configuré sur le serveur central.
-   Communauté SNMP & Version : indique le nom de la communauté et la version SNMP.
-   Surveillé depuis le collecteur : indique quel serveur supervisera cet hôte.
-   Modèles : Ajouter un ou plusieurs modèles d’hôte pour cet hôte.
-   Créer aussi les services liés au modèle : Si positionné sur « oui », Centreon génèrera automatiquement les services en se basant sur les modèles de services liés aux modèles d’hôtes définis au-dessus.
-   Commande de vérification : indique la commande à utiliser pour vérifier si l’hôte est disponible.
-   Arguments : Indique les arguments fournis à la commande de vérification
    -   Chaque argument commence avec un « ! ».
-   Macros : Ajout de macros personnalisées.
    -   Nom de la macro et Valeur de la macro : Définissent le nom et la valeur de la macro.
    -   Mot de passe : Cache la valeur de la macro.
-   Période de contrôle : indique la période de contrôle de l’hôte par l’ordonnanceur (ex : aucun, 24h/24, pendant les heures de travail, etc.)
-   Nombre de contrôles avant validation de l’état : indique le nombre de contrôle qui seront fait avant de valider le statut de l’hôte. Lorsque le statut est validé, le processus de notification est enclenché.
-   Intervalle normal de contrôle : Indique l’intervalle entre chaque nouvelle vérification quand le statut de l’hôte est positionné sur « OK » (exprimé en minutes).
-   Intervalle non-régulier de contrôle : indique l’intervalle de validation du statut non-OK de l’hôte (exprimé en minutes).
-   Contrôles actifs activés et Contrôles passifs activés : activer ou désactiver les contrôles actifs et passifs.

#### Notification

-   Notification activée : Activer ou désactiver les notifications pour cet hôte.
-   Options de notifications : Indiquent les statuts pour lesquels une notification sera envoyée.
-   Intervalle de notification : Indique le temps en minute entre chaque envoi de notification si le statut est « non-OK ». Lorsque la valeur est définie à 0, l’ordonnanceur enverra une seule notification par changement de statut.
-   Période de notification : Indique la période pendant laquelle les notifications seront activées.
-   Délai de première notification : Indique le délai en minute à respecter avant l’envoi d’une première notification si le statut non-OK est validé.
-   Contacts liés : Indique le ou les contacts qui recevront les notifications.
-   Groupe de contacts liés : Tous les contacts appartenant aux groupes de contacts définis recevront les notifications.

#### Relations

-   Groupes d’hôtes parents : Indique les groupes d’hôtes auxquels l’hôte appartient.
-   Catégorie d’hôtes parents : Indique les catégories auxquelles l’hôte appartient.
-   Hôtes parents : Défini les relations physiques de parenté entre objet.
-   Hôtes enfants : Défini les relations physiques de parenté entre objet.

#### Traitement de données

-   Contrôle de vérification de l’hôte : Si activé, la commande de remontée des contrôles de l’hôte sera activée.
-   Contrôler la fraîcheur du résultat : Activer ou désactiver le contrôle de fraîcheur du résultat.
-   Seuil de fraîcheur du résultat : Si durant une période exprimée en seconde aucune demande de changement de statut de l’hôte n’a été reçue alors la commande de vérification active est exécutée.
-   Détection de bagotage des statuts : Activer ou désactiver la détection du bagotage des statuts, c’est-à-dire les statuts changeant trop fréquemment de valeur sur une période donnée.
-   Seuil bas de détection de bagotage des statuts / Seuil haut de détection de bagotage des statuts : Indiquent les seuils hauts et bas pour la détection du bagotage en pourcentage de changement de statuts.
-   Traitement des données de performances : Activer ou désactiver le traitement des données de performances. Inutile si Centreon Broker est utilisé.
-   Rétention des informations de statut / Rétention des informations ne concernant pas le statut : Indiquent si les informations concernant ou non le statut sont sauvegardées après chaque relance de la commande de vérification.
-   Options à enregistrer : indique les options à enregistrer si la rétention est activée.
-   Gestionnaire d’évènements activé : Activer ou désactiver le gestionnaire d’évènements.
-   Gestionnaire d’évènements : Indique la commande à exécuter si le gestionnaire d’évènements est activé.
-   Arguments : Indique les arguments de la commande du gestionnaire d’évènements.

#### Informations détaillées de l’hôte

-   URL : Indique l’URL qui peut être utilisée pour donner davantage d’informations sur l’hôte.
-   Notes : Ajouter des notes optionnelles concernant l’hôte.
-   URL d’action : Indique l’URL habituellement utilisée pour donner des informations d’actions sur l’hôte (ex : maintenance).
-   Icône : Permet d’indiquer l’icône à utiliser pour l’hôte.
-   Icône alternative : Texte utilisé si l’icône ne peut être affichée.
-   Niveau de criticité : Indique le niveau de criticité de l’hôte.

Les champs présentés ci-dessous sont des champs utilisés uniquement par la CGI de l’ordonnanceur (habituellement Nagios). Par conséquent, ils présentent peu d’intérêt lorsqu’on utilise Centreon Engine et Centreon Broker. 

-   Image de la carte des états : Indique le logo pour la CGI de l’ordonnanceur.
-   Coordonnées géographiques : Indique les coordonnées géographiques pour géolocaliser l’élément (utile dans Centreon Map).
-   Coordonnées 2D et 3D : Indiquent les coordonnées 2D et 3D utilisées par la CGI.
-   ACL Resource Groups : Lier l’hôte à un groupe d’hôtes afin de pouvoir visualiser ce dernier (seulement visible pour les utilisateurs non administrateur)

### Ajouter un hôte

#### Onglet « host configuration »

A l’aide des informations précédemment citées, dans un premier temps remplir et configurer les champs suivants : 

-   Nom
-   Alias 
-   Adresse IP / DNS 
-   La communauté SNMP et la version du protocole SNMP à utiliser
-   A la ligne Template : cliquer sur « ajouter une nouvelle entrée » en cas d’ajout d’un Template à l’hôte

![](/images/hôtes_centreon-4.png)

![](/images/hôtes_centreon-5.png)

-   Positionner à OUI la partie « créer aussi les services liés aux modèles » pour que l’ajout du ou des Templates soit effectif

![](/images/hôtes_centreon-6.png)

Choisir la période de check désirée (de préférence 24x7 pour une supervision 24h/24 et 7j/7)  
Indiquer également le : 

-   Max check attempts
-   Normal check Interval
-   Retry check interval

![](/images/hôtes_centreon-7.png)

#### Onglet « Notification »

A la ligne « Notification enabled » mettre OUI pour permettre l’activation des notifications par email en cas de problème.  
Dans « Linked Contacts » choisir quel utilisateur doit être averti par email.  
Il est également possible de sélectionner un groupe d’utilisateur à contacter par email à l’aide de la ligne « Linked Contact Group ».

Ensuite, dans la partie « Notification Options » choisir le type de notification qui doit être activé puis dans la partie « Notification Period », choisir la période de check désirée.  
Laisser les autres options par défaut.

![](/images/hôtes_centreon-8.png)

#### Onglet « Relation »

Cet onglet permet de configurer une relation « parent » ou « enfant » avec d’autres hôtes ou des groupes d’hôtes.  
Il ne sera pas détaillé dans cette procédure.

#### Onglet « Data Processing »

Cet onglet permet de configurer les informations nécessaires au traitement des données.  
Il ne sera pas détaillé dans cette procédure.

#### Onglet « Host Extended Info »

Cet onglet permet de configurer les informations nécessaires à la création de graphiques.  
Il ne sera pas détaillé dans cette procédure.

Cliquer sur « sauvegarder »  
L’hôte est maintenant défini et visible dans l’interface Web de Centreon.

![](/images/hôtes_centreon-9.png)

Toutefois, il n’est pas encore connu par le superviseur, il faudra déployer la configuration.