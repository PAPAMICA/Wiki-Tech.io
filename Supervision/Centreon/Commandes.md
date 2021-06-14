---
title: Les commandes
description: Les commandes dans Centreon
published: true
date: 2021-06-14T08:13:03.959Z
tags: 
editor: markdown
dateCreated: 2021-05-24T10:37:02.821Z
---

![Centreon | Supervision Informatique : Infrastuctures Hybrides, Cloud, AIOps](https://static.centreon.com/wp-content/uploads/2019/10/Centreon_Logo_RVB_HD_Cut.png?x55598)

# Les Commandes

## Qu’est-ce qu’une commande

Une commande est la définition d’une ligne de commande qui utilise un script ou une application afin de réaliser une action. Il est possible d’exécuter cette commande en précisant des arguments.

Il existe trois types de commandes :

-   Les commandes de vérification sont utilisées par les ordonnanceurs afin de vérifier le statut d’un hôte ou d’un service.
-   Les commandes de notification sont utilisées par les ordonnanceurs pour alerter les contacts (via mail, SMS...).
-   Des commandes diverses sont utilisées par les modules complémentaires (pour effectuer certaines actions), par l’ordonnanceur pour le traitement des données...

## Les différents champs

-   Nom de la commande : définit le nom de la commande.
-   Type de commande : permet de choisir le type de commande.
-   Ligne de commande : indique l’application ou le script utilisé avec la commande.
-   Activer le Shell : permet d’activer des fonctions propres à un Shell tel que le « pipe », etc.
-   Exemple d’arguments et $HOSTADDRESS$ : définissent des exemples d’arguments et une adresse IP pour faire un test.
-   Description des arguments : permet d’ajouter une description aux arguments de type “$ARGn$”.
-   Effacer les arguments : efface la description des arguments définie
-   Description des macros : permet d’ajouter une description aux macros personnalisées (arguments personnalisés).
-   Connecteurs : permet de lier un connecteur à la commande.
-   Modèle de graphique : permet de lier la commande à un modèle de graphique.
-   Commentaire : permet de commenter la commande.

## Création d’une commande « basique » (Check)

Il existe de nombreuses commandes disponibles dans Centreon.  
Il est toutefois possible de créer soit même des scripts mais également d’installer des plugins pour avoir de nouvelles commandes selon les différents besoin de vérification et de supervision à mettre en place.  
Avant de commencer à installer des plugins, il est conseillé de commencer par certaines commandes « basiques ». Elles permettront soit de se faire la main si on ne connaît pas Centreon soit de vérifier que tout fonctionne correctement.  
Parmi ce type de commandes et à titre d’exemple, nous pouvons citer des checks sur : 

-   La RAM
-   Le ou les Disques dur (fonctionnement, accessibilité, espace disponible, etc.)
-   L’accès réseau
-   Les températures
-   Etc.

A titre d’exemple dans cette procédure, une commande sera créée pour vérifier l’utilisation en pourcentage du CPU.  
Afin de créer une commande, il faut cliquer sur « configuration » puis sur « commandes »

![](/images/commandes_centreon-1.png)

Sur la gauche, cliquer sur le type de commande à créer

![](/images/commandes_centreon-2.png)

A titre d’exemple pour cette procédure, une commande de vérification de la charge CPU sera créé.  
Cliquer sur « Ajouter »  
Ecrire le nom de la commande.  
La partie de droite va permettre de créer la commande à l’aide d’information préenregistrées. Il est également possible de créer ses propres commandes personnalisées.  
Dans la partie de droite, commencer par cliquer sur la ligne du haut pour choisir $USER1$ puis cliquer sur « << » pour l’ajouter à la commande.

![](/images/commandes_centreon-3.png)

La partie du milieu va elle permettre de choisir le « check » que l’on souhaite mettre en place. De la même façon, choisir celui qui va être utilisé et cliquer sur « << » pour l’ajouter à la commande.

![](/images/commandes_centreon-4.png)

Ensuite il faut définir l’hôte qui sera ciblé. Pour cela écrire -H puis dans la ligne du bas choisir la variable qui correspond à l’adresse IP de l’hôte : $HOSTADDRESS$  
L’ajouter à la commande de la même manière que précédemment.  
Attention il est important de bien respecter la nomenclature et de ne pas tout coller dans la commande.  
A partir du moment où le check a été positionné il faudra mettre un espace entre chaque nouvel ajout.

![](/images/commandes_centreon-5.png)

A ce stade, la commande n’est pas complète. Pour l’instant, il a été déclaré à l’aide de différentes variables : 

-   Le chemin où se trouve le fichier de configuration du check
-   Le check utilisé
-   L’hôte ciblé

Il n’a pas été défini sur quels critères le check va s’appuyer pour remonter des informations.  
Il manque donc les arguments à positionner afin de pouvoir définir un seuil à vérifier pour prévenir l’administrateur en cas de problème.  
Pour rajouter des arguments à une commande, toujours dans la ligne du bas, sélectionner $ARGn$ et l’ajouter à la commande à l’aide de « << ». Répéter cette opération autant de fois qu’il y a d’arguments à la commande. Attention le « n » est à remplacer par un numéro pour définir le numéro de l’argument (ex : $ARG1$)  
Pour la vérification du CPU, il est conseillé de définir deux types d’alertes :  les « warning » et les « critical ». Pour cela, tout comme l’ajout du -H dans la commande nous allons ajouter « -w » et « -c » afin de définir un seuil d’avertissement et critique.

![](/images/commandes_centreon-6.png)

Dans cet exemple, une alerte « warning » sera envoyée par email à l’administrateur si le charge du CPU dépasse X% et une alerte « critique » sera envoyée si la charge CPU dépasse Y%.  
Les valeurs des arguments seront positionnées et abordées plus tard dans ce document.  
Chaque check peut avoir des arguments différents, il est donc utile de préciser la définition des arguments pour chaque commande créée. Pour cela, cliquer sur « Describe arguments ». Un pop-up va s’ouvrir et permettre d’écrire à quoi correspondent les arguments. Une fois indiqué, cliquer sur Save, la définition des arguments devient visible dans la commande.

![](/images/commandes_centreon-7.png)

Cette description sera visible lors de l’utilisation de la commande dans un formulaire de création d’hôte ou de service.  
Une fois la commande préparée et avant de la sauvegarder, il est possible de la tester.  
Pour cela, remplir le champ « Arguments exemple » avec les informations souhaitées.

![](/images/commandes_centreon-8.png)

Dans la partie de gauche, il va être indiqué le seuil du warning et celui du critical. Les informations devront commencer, être séparées et finir par « ! »  
Dans la partie de droite l’adresse IP du PC ou serveur ciblé. 

![](/images/commandes_centreon-9.png)

Cliquer sur la flèche bleue pour lancer le test. Un pop-up avec le résultat du test va apparaitre.  
Si tout s’est déroulé correctement le status OK sera visible

![](/images/commandes_centreon-10.png)

Cliquer sur Save pour enregistrer la commande et sa configuration.  
Elle sera visible dans la liste des commandes depuis le serveur Centreon.

![](/images/commandes_centreon-11.png)

Pour créer d’autres commandes « basiques », recommencer cette partie de la procédure et choisir un autre check à mettre en place. Autres exemples de commandes.

-   Pour vérifier l’accès réseau d’un périphérique il sera possible d’utiliser la commande suivante : 

```plaintext
$USER1$/check_centreon_ping -H $HOSTADDRESS$ -w $ARG1$ -c $ARG2$ -n $ARG3$
```

Dans cette commande, les arguments 1 et 2 seront les indicateurs « warning » et « critical ». Toutefois, contrairement à la supervision du CPU, le contenu de ces arguments devra être écrit de cette façon par exemple : « 300,50% ».  
Ici on indique que si le temps dépasse 300ms OU si on perd 50% des paquets alors l’alerte se déclenchera (les chiffres sont à titre informatifs et à configurer selon les besoins).  
La partie « *\-n $ARG3$* », quant à elle servira à indiquer le nombre de paquets envoyé pour effectuer le ping.

-   Pour vérifier l’utilisation de la RAM en pourcentage d’un périphérique il sera possible d’utiliser la commande suivante : 

```plaintext
$USER1$/check_centreon_snmp_memory -H $HOSTADDRESS$ -w $ARG1$ -c $ARG2$ -C $ARG3$ -v2c
```

Dans cette commande, les arguments 1 et 2 seront les indicateurs « warning » et « critical. Ils seront utilisés afin de déterminer à partir de quel pourcentage d’utilisation de la RAM les alertes se déclencheront.   
La partie « *\-C $ARG3$ -v2c »*, quant à elle servira à indiquer le nom de la « communauté SNMP » ainsi que la version du protocole SNMP qui seront utilisés pour la supervision.