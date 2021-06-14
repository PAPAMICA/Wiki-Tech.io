---
title: Les plugins
description: Les commandes à l'aide de plugins
published: true
date: 2021-06-14T08:13:27.750Z
tags: 
editor: markdown
dateCreated: 2021-05-24T10:37:15.994Z
---

![Centreon | Supervision Informatique : Infrastuctures Hybrides, Cloud, AIOps](https://static.centreon.com/wp-content/uploads/2019/10/Centreon_Logo_RVB_HD_Cut.png?x55598)

# Mise en place de commandes (checks) à l’aide de plugins

En plus des différents checks disponibles par défaut dans Centreon, il existe des plugins, gratuit ou payant, que l’on peut télécharger.  
Centreon étant un outil de supervision open source et communautaire, il est également possible de trouver des plugins gratuits mis à disposition par la communauté.  
Une autre solution est de créer directement ses propres plugins, à condition d’avoir les connaissances nécessaires.

## Configuration des checks a partir des Packs de Plugins inclus dans Centreon

### Ajout de Plugins

Centreon est un excellent outil de supervision qui peut être configuré manuellement pour correspondre exactement aux différents besoins de l’entreprise. Toutefois, il existe des packs de plugins contenant des modèles de configuration.  
Ces différents packs réduisent le temps nécessaire pour installer et superviser la plupart des services de l’entreprise.  
Pour fonctionner, les composants techniques Centreon License Manager et Centreon Plugin Pack Manager sont nécessaires.\*

L’installation de ces deux paquets est très simple.  
En fonction de la version de Centreon utilisée, il sera nécessaire soit d’installer en ligne de commande ces deux packages soit directement de les installer depuis l’interface graphique.

Pour commencer se rendre dans la section « Administration » puis « Extensions » et enfin « Modules ».  
Vérifier si les deux modules sont présents. 

![](/images/plugins_centreon-5.png)

S’ils n’y sont pas, il faudra installer les packages en ligne de commande avant de poursuivre.  
Pour effectuer l’installation des packages en ligne de commande, se connecter sur le serveur Centreon avec l’utilisateur root en SSH et de taper la commande suivante.

```plaintext
yum install centreon-pp-manager
```

Cette commande téléchargera Centreon Plugin Pack Manager et également Centreon License Manager en tant que dépendance.  
Une fois ces deux paquets téléchargés et présent dans « Modules », il suffira de les installer.  
Le premier module à installer et activer sera Centreon License Manager : cliquer sur le nom du module puis sur « install moduel »

![](/images/plugins_centreon-6.png)

Puis il faudra installer et activer Centreon Plugin Pack Manager : cliquer sur le nom du module puis sur « install moduel »

![](/images/plugins_centreon-7.png)

Lorsque les deux modules seront installés, il sera désormais possible de se rendre dans la section « Configuration » puis « Plugin packs » pour trouver les 6 plugins gratuits proposés. Si on s’inscrit sur le site de Centreon, 5 autres plugins packs deviennent gratuits, les autres étant payant.

Il existe plus de 150 plugins packs disponibles dans l’offre IMP.

![](/images/plugins_centreon-8.png)

### Activation des modules selon les besoins

Comme indiqué précédemment, suite à l’installation des deux modules, des packs de plugins sont maintenant disponible.  
Pour activer les plugins, se rendre dans « Configuration » puis « Plugin packs », puis cliquer sur le plugin à installer.  
Dans le cadre de cette procédure, le plugin de check SNMP de Linux sera installé 

![](/images/plugins_centreon-9.png)

Cliquer sur le plugin pour afficher les détails 

![](/images/plugins_centreon-10.png)

Pour l’installer il suffit de cliquer sur l’icone **+**  
Patienter pour que le plugin s’installe automatiquement.  
Une fois installé l’icône du plugin affiche une marque, confirmant l’installation 

![](/images/plugins_centreon-11.png)

Les différents checks et Templates seront à présent disponibles dans leurs sections respectives.

### Utilisation des checks fournis par les modules

Pour utiliser les différents checks inclus dans les packs de modules il faut absolument garder en tête la notion d’héritage qui a été précédemment expliqué.  
En effet, les commandes, Templates de services et Templates d’Hôtes sont déjà liés les uns aux autres lors de l’installation d’un pack.

_Petit rappel :_ 

![](/images/checks_centreon.png)

Généralement, lors de l’installation d’un pack, cette notion de hiérarchie et d’héritage ne sera pas retouchée mais plutôt utilisée.  
Il sera toutefois nécessaire de customiser les valeurs des « warning », « critical » et autres valeurs à vérifier.  
Pour les besoins de cette procédure voici un exemple.  
Lors de l’’installation des packs « Linux SNMP » ou « Windows SNMP », le Template de service « Base-Ping-LAN » devient accessible.

![](/images/plugins_centreon-12.png)

Ce Template va permettre d’effectuer la supervision de la connectivité réseau à l’aide du ping pour les hôtes liés à ce Template. Toutefois il est nécessaire de le configurer selon ses besoins, le nombre de paquets envoyés ou les valeurs pour le « warning » et « critical » ne sont pas forcément celles que l’on souhaite. Il est également important de positionner correctement l’ensemble des périodes de check, des intervalles de supervision ainsi que les notifications comme indiqué précédemment dans cette procédure.

En effet par défaut, tout est vide.

![](/images/plugins_centreon-13.png)

### Personnalisation des Checks à partir de ceux des plugins

Une fois les plugins installés, un certain nombre de commandes et de Templates sont disponibles.  
Nous avons vu comment les utiliser mais il est également possible si on le souhaite de ne pas utiliser les Templates prédéfinies et de recréer ses propres Templates à partir des commandes (checks) disponibles.  
Pour cela il faudra procéder de la même façon que précédemment dans cette procédure.

## Configuration des checks à partir des packs de plugins fournis par la communauté

Il existe de nombreux sites proposant des listes de plugins mis à disposition gratuitement.  
Par exemple, le site « exchange Nagios » est plutôt bien fourni : [https://exchange.nagios.org](https://exchange.nagios.org)  
Toute fois pour les besoins de cette procédure, nous allons prendre en exemple le plugin « centreon\_plugins.pl ».  
Il s’agit d’un plugin écrit en perl qui est très complet et qui permet de mettre en place un très grand nombre de checks différents.

### Installation de « centreon\_plugins.pl »

Avant toute chose, si ce n’est pas déjà fait il faut installer la commande « git » 

```plaintext
yum install git
```

Une fois la commande installée, se déplacer dans le répertoire /tmp puis télécharger le plugin depuis [Github](https://github.com/centreon/centreon-plugins) à l’aide des commandes ci-dessous :

```plaintext
cd /tmp
git clone https://github.com/centreon/centreon-plugins
```

Le répertoire « centreon-plugins » devrait être visible dans /tmp

```plaintext
ll
drwxr-xr-x 17 root root 309 27 août  16:24 centreon-plugins
```

Déplacer le dossier dans /usr/lib/nagios/plugins/ puis lui ajouter les droits nécessaires pour son exécution par centreon 

```plaintext
cp centreon-plugins /usr/lib/nagios/plugins/
cd /usr/lib/nagios/plugins
chmod 755 -Rf centreon-plugins
chown -Rf centreon:centreon centreon-plugins
```

### Utilisation du plugin « centreon\_plugins.pl »

Avant de commencer, voici le contenu du dossier :

```plaintext
drwxr-xr-x 47 centreon centreon  4096 16 juil. 12:47 apps
drwxrwxr-x  4 centreon centreon    35 16 juil. 12:47 centreon
-rwxr-xr-x  1 centreon centreon   989 16 juil. 12:34 centreon_plugins.pl
-rwxr-xr-x  1 centreon centreon 15135 16 juil. 12:34 changelog
drwxr-xr-x  7 centreon centreon    77 16 juil. 12:47 cloud
drwxr-xr-x  2 centreon centreon    46 16 juil. 12:47 contrib
drwxr-xr-x 10 centreon centreon   119 16 juil. 12:47 database
drwxr-xr-x  4 centreon centreon    26 16 juil. 12:47 docs
drwxr-xr-x  4 centreon centreon   107 16 juil. 12:47 example
drwxr-xr-x 10 centreon centreon   110 16 juil. 12:47 hardware
-rwxrwxr-x  1 centreon centreon  2752 20 août  10:24 host-email.php
-rwxr-xr-x  1 centreon centreon 11357 16 juil. 12:34 LICENSE.txt
drwxr-xr-x 63 centreon centreon  4096 16 juil. 12:47 network
drwxr-xr-x  5 centreon centreon    48 16 juil. 12:47 notification
drwxr-xr-x  8 centreon centreon    87 16 juil. 12:47 os
-rwxr-xr-x  1 centreon centreon  3194 16 juil. 12:34 README.md
-rwxrwxr-x  1 centreon centreon  5180 20 août  10:24 service-email.php
drwxr-xr-x  3 centreon centreon    18 16 juil. 12:47 snmp_standard
drwxr-xr-x 23 centreon centreon   305 16 juil. 12:47 storage
```

_A savoir :_

-   Les parties en bleues sont des dossiers contenant les informations que le plugin va utiliser lors des différents checks.
-   Les parties en vert sont des exécutables. Celui qui nous intéresse et que nous utiliserons est « centreon\_plugins.pl ».

Précédemment nous avons vu que lorsque nous utilisions une commande, la variable « *$USER1$* » était utilisée.  
Elle correspond au chemin suivant : */usr/lib/nagios/plugins/* et permet de faire appel aux commandes se trouvant dans ce répertoire.  
Par exemple : */usr/lib/nagios/plugins/check\_centreon\_snmp\_memory* en y associant les valeurs nécessaires au check permettra d’effectuer un check de la RAM sur le ou les appareils ciblés.

```plaintext
$USER1$/check_centreon_snmp_memory -H $HOSTADDRESS$ -C $ARG1$ -v $ARG2$ -w $ARG3$ -c $ARG4$
```

L’exécutable « centreon\_plugins.pl » s’utilise de la même façon cependant il se trouve dans */usr/lib/nagios/plugins/centreon-plugins/*  
Afin d’utiliser ce plugin nous allons donc créer une nouvelle ressource.  
Pour cela aller dans Configuration puis « pollers » puis « ressources » et cliquer sur « add »

![](/images/plugins_centreon-14.png)

Remplir les informations comme sur la capture d’écran et cliquer sur « save »

![](/images/plugins_centreon-15.png)

Les commandes qui seront enregistrées dans Centreon commenceront donc par *$USER3$/centreon\_plugins.pl*  
Maintenant que tout est prêt, nous allons faire _AVANT TOUT_ un test directement depuis la CLI de centreon.  
Commencer par faire une recherche des différents plugins Windows à disposition.

```plaintext
cd /usr/lib/nagios/plugins/centreon-plugins
./centreon_plugins.pl --list-plugin | grep os | grep windows
PLUGIN: os::windows::snmp::plugin
PLUGIN: os::windows::wsman::plugin
PLUGIN: os::windows::local::plugin
```

Il est également possible d’effectuer des recherches dans chacun des différents répertoires précédemment cités : *apps, centreon, cloud, contrib, database, docs, Example, hardware, Network, notification, Os, snmp\_standard, storage*

La marche à suivre sera la même :

```plaintext
./centreon_plugins.pl --list-plugin | grep database
PLUGIN: database::informix::plugin
PLUGIN: database::oracle::plugin
PLUGIN: database::mysql::plugin
PLUGIN: database::mssql::plugin
PLUGIN: database::firebird::plugin
            --database
PLUGIN: database::sap::hana::plugin
    --database
PLUGIN: database::postgres::plugin
            --database
PLUGIN: database::sybase::plugin
```

Pour revenir à Windows, le plugin qui nous intéresse et qui permettra de faire des checks en snmp est le PLUGIN: *os::windows::snmp::plugin*  
Il s’agit en réalité d’une arborescence qui pourrait être vue de cette manière : *os/windows/snmp/plugin/*  
Chaque plugin comprend différents modes d’exécution qui peuvent être utilisés. Ces modes peuvent être vu comme des catégories.  
Pour afficher la liste des modes de Windows, effectuer la commande suivante

```plaintext
./centreon_plugins.pl --plugin=os::windows::snmp::plugin --list-mode

Plugin Description:
    Check Windows operating systems in SNMP.

Global Options:
    --mode  Choose a mode.

    --dyn-mode
            Specify a mode with the path (separated by '::').

    --list-mode
            List available modes.

    --mode-version
            Check minimal version of mode. If not, unknown error.

    --version
            Display plugin version.

Modes Available:
   cpu
   interfaces
   list-interfaces
   list-storages
   memory
   processcount
   service
   storage
   swap
   time
   uptime
```

La liste affichée en vert (ça ne sera pas le cas sur centreon) correspond aux modes disponibles pour superviser un hôte Windows.  
A titre d’exemple pour cette procédure, le mode CPU sera utilisé.  
Commencer par afficher l’aide pour voir les arguments à utiliser 

```plaintext
./centreon_plugins.pl --plugin=os::windows::snmp::plugin --mode=cpu --help
```

Une liste détaillée comprenant 4 grandes parties va apparaitre (le détail ne sera pas repris ici) :

-   *Global Options* : les options globales
-   *Output Options* : les options de sortie
-   *Snmp Options* : Les options SNMP (Communauté, version….)
-   *Mode* : les arguments que l’on peut rajouter lors de la vérification

Maintenant qu’on a vu les différentes options, passons à l’exécution de la commande

```plaintext
./centreon_plugins.pl --plugin=os::windows::snmp::plugin --mode=cpu --hostname=10.0.0.8 --snmp-community=public --snmp-version=2c
OK: 2 CPU(s) average usage is: 40.00% | 'cpu0'=40.00%;;;0;100 'cpu1'=40.00%;;;0;100 'total_cpu_avg'=40.00%;;;0;100
```

*\--plugin=os::windows::snmp::plugin                -> Le chemin du plugin utilisé dans la commande*  
*\--mode=cpu                                                         -> le mode utilisé par le plugin*  
*\--hostname=10.0.0.8                                         -> le nom FQDM ou l’ip du serveur à sonder (peut varier selon le mode choisi)*  
*\--snmp-community=public                                -> la communauté SNMP configurée sur le serveur (peut varier selon le mode choisi)*  
*\--snmp-version=2c                                              -> la version du protocole SNMP qui sera utilisé*

Il est possible d’ajouter des avertissements à la commande afin de définir un niveau d’alerte à l’aide de --*warning=X* et --*critical=Y* ( X et Y sont des pourcentages)

```plaintext
./centreon_plugins.pl --plugin=os::windows::snmp::plugin --mode=cpu --hostname=10.0.0.8 --snmp-community=public --snmp-version=2c --warning=75 --critical=90
OK: 2 CPU(s) average usage is: 42.00% | 'cpu0'=41.00%;;;0;100 'cpu1'=43.00%;;;0;100 'total_cpu_avg'=42.00%;0:75;0:90;0;100
```

Les tests fait directement depuis la CLI de centreon étant concluant, passons à la création de commandes depuis l’interface graphique.  
Si l’on souhaite vérifier l’utilisation des CPU il est tout à fait possible de reprendre la commande précédemment utilisée, ce qui donnera : 

```plaintext
$USER3$/centreon_plugins.pl --plugin=os::windows::snmp::plugin --mode=cpu --hostname=$HOSTADDRESS$ --snmp-community=$ARG1$ --snmp-version=$ARG2$ --warning=$ARG3$ --critical=$ARG4$
```

Les variables seront à définir dans le Template de service :

*$HOSTADDRESS$                 -> IP*  
*$ARG1$                                 -> la communauté SNMP*  
*$ARG2$                                 -> la version du protocole SNMP qui sera utilisée*  
*$ARG3$                                 -> valeur à définir pour une alerte de type « warning »*  
*$ARG4$                                 -> valeur à définir pour une alerte de type « critique »*

Cependant toujours à des fins d’exemple pour cette procédure, nous allons effectuer une commande utilisant des « macros personnalisées » afin de vérifier le trafic des interfaces réseau de nos serveurs.

Les macros standards sont prédéfinies dans le code source des moteurs de supervision. Ces différentes macros permettent de récupérer la valeur de différents objets au sein des commandes. Une macro commence et se termine toujours par le signe « **$** ».  
Une liste complète des macros est disponible à [**cette adresse**](https://assets.nagios.com/downloads/nagioscore/docs/nagioscore/3/en/macrolist.html)

Commencer par se connecter à l’interface graphique de Centreon puis cliquer sur « pollers », « ressources » puis « add » 

![](/images/macro_centreon-1.png)

Les macros personnalisées vont dépendre de ce qui sera à superviser mais voici une liste qui peut être utile de créer :

-   $\_SERVICEMODE$
-   $\_SERVICEDISK$
-   $\_SERVICEHOST$
-   $\_SERVICEINTERFACE$
-   $\_SERVICEOPTION$
-   $\_SERVICEUSERNAME$
-   $\_SERVICEPASSWORD$
-   $\_SERVICEPORT$
-   $\_SERVICEPROCESS$
-   $\_SERVICESID$
-   $\_SERVICECOMMAND$
-   $\_SERVICESUBCOMMAND$
-   $\_SERVICETIMEOUT$
-   $\_SERVICEWARNING$
-   $\_SERVICEWARNINGIN$
-   $\_SERVICEWARNINGOUT$
-   $\_SERVICECRITICAL$
-   $\_SERVICECRITICALIN$
-   $\_SERVICECRITICALOUT$

![](/images/macro_centreon-2.png)

Une fois les macros créées et sauvegardées, cliquer sur « configuration » puis sur « commands » puis sur « check » et enfin sur « add »

![](/images/macro_centreon-3.png)

![](/images/macro_centreon-4.png)

Commencer par nommer la commande 

![](/images/macro_centreon-5.png)

Puis écrire la commande dans la section « command line »

```plaintext
$USER3$/centreon_plugins.pl --plugin=$_SERVICEPLUGIN$ --hostname=$HOSTADDRESS$ --snmp-community=$_HOSTSNMPCOMMUNITY$ --snmp-version=$_HOSTSNMPVERSION$ --mode=$_SERVICEMODE$ --interface=$_SERVICEINTERFACE$ --warning-in-traffic=$_SERVICEWARNINGIN$ --warning-out-traffic=$_SERVICEWARNINGOUT$ --critical-in-traffic=$_SERVICECRITICALIN$ --critical-out-traffic=$_SERVICECRITICALOUT$ $_SERVICEOPTION$ $_SERVICEOPTION$
```

Cliquer sur le bouton « Describe macros »    
Un pop-up va alors s’ouvrir permettant de décrire les différentes macros qui seront utilisées  
Attention, les informations utilisées ici le sont à titre d’exemple afin d’avoir une bonne visibilité de la commande. Les valeurs qui seront réellement utilisées seront définies dans le Template de service qui sera créée par la suite.

![](/images/macro_centreon-6.png)

Cliquer sur « save »  
Les informations apparaissent alors dans la commande.

![](/images/macro_centreon-7.png)

Valider la création de la commande en cliquant sur « save »

Les serveurs ayant plusieurs interfaces, il nous faut dans un premier temps savoir quelle est l’ID de l’interface que l’on souhaite superviser. Pour cela effectuer la commande suivante directement dans la CLI de centreon : 

```plaintext
./centreon_plugins.pl --plugin=os::windows::snmp::plugin --mode=list-interfaces --hostname=172.20.0.4 --snmp-community=**** --snmp-version=2c --interface= --name --oid-display=ifDesc --filter-status=up
```

La liste des interfaces apparait il ne reste plus qu’à choisir l’interface ciblée 

```plaintext
List interfaces:
'Software Loopback Interface 1' [speed = 1073, status = up, id = 1]
Skipping interface 'WAN Miniport (IKEv2)': no matching filter status
'Broadcom BCM5708C NetXtreme II GigE (NDIS VBD Client) #46' [speed = 1000, status = up, id = 7]
```

Dans notre exemple l’ID de l’interface est «7 ». C’est cette valeur qui sera utilisée afin de récolter des informations lors de l’utilisation du check

Passons maintenant à la création du Template de service qui utilisera cette commande.

Cliquer sur « Configuration » puis « Services » puis sur « Templates » et en fin sur « add »  
De la même manière que vu précédemment lors de la création de Templates, nous allons remplir les informations mais cette fois-ci ce seront les « macros personnalisées » qui seront utilisées et définies

![](/images/macro_centreon-8.png)

![](/images/macro_centreon-9.png)

Cliquer sur « save » pour enregistrer la Template  
Cliquer sur « Configuration » puis « Hosts » puis sur « Templates » et en fin sur « add »  
A nouveau, de la même manière que vu précédemment lors de la création de Templates, nous allons créer un Template d’hôte dédiée à la vérification du trafic.  
Remplir les différentes informations puis dans la section « relations » choisir le Template de service précédemment créée pour l’associer avec ce Template d’host.

Cliquer sur « save » pour enregistrer le Template  
Il ne reste plus qu’à associer ce Template avec un hôte.