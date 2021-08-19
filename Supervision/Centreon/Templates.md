---
title: Les templates
description: Fonctionnement des templates
published: true
date: 2021-06-14T08:13:17.092Z
tags: 
editor: markdown
dateCreated: 2021-05-24T10:37:20.485Z
---

![Centreon | Supervision Informatique : Infrastuctures Hybrides, Cloud, AIOps](https://static.centreon.com/wp-content/uploads/2019/10/Centreon_Logo_RVB_HD_Cut.png?x55598)

# Les Templates 

## Qu’est-ce qu’un Template

Un « Template » ou « modèle » est une pré-configuration de paramètres d’un objet (hôte, service, etc.) qui pourra être utilisé pour le configurer. Le principal avantage est de pouvoir définir des valeurs par défaut pour certains objets afin d’accélérer la création d’objets similaires. Lors de la création d’un Template, le nom est le seul paramètre obligatoire, tous les autres sont optionnels.

Les avantages :

-   Définition simplifiée des éléments
-   Pas de redondance d’information
-   Facilité d’ajout de nouvelles ressources
-   Configurations prédéfinies assimilées à un « catalogue d’indicateurs »
-   Les modèles peuvent hériter d’autres modèles

Il existe trois types de modèles :

-   Les modèles d’hôtes
-   Les modèles de services
-   Les modèles de contacts

Un hôte ou un Template d’hôte peut hériter une configuration depuis un ou plusieurs Template modèles d’hôtes.

### Les Templates de services (pour les commandes)

Un service ou un Template de service ne peut hériter que d’un seul Template de service.  
L’héritage dans ce cas est dit de type « Père-Fils ».  
Il s’agit d’une prédéfinition de paramètres à “n” niveaux. L’objet hérite de son modèle qui peut lui-même hériter de son modèle.  
Si le fils redéfini un paramètre déjà existant dans un modèle « père » alors la modification écrase le paramètre défini dans les modèles de niveaux supérieurs.  
Dans le cas contraire, les paramètres de l’objet viennent compléter le paramétrage « père ».  
Les paramètres au plus proche de l’objet sont prioritaires.

Schéma explicatif : 

![](/images/schema_template.png)

### Les Templates d’hôtes

Un hôte ou un Template d’hôte peut hériter d’un ou plusieurs Template d’hôtes.   
Cet héritage peut être :

-   De type associatif (addition de plusieurs modèles d’hôte)
-   De type père-fils

Un héritage dit « associatif » consiste à additionner plusieurs modèles au sein d’un même objet afin d’additionner l’ensemble des paramètres disponibles.

![](/images/schema_template-2.png)

Toutefois si un hôte hérite de plusieurs Template d’hôtes et qu’un même paramètre est défini sur plusieurs Template, alors le modèle d’hôte situé au-dessus des autres modèles est prioritaire par rapport à ses ascendants.

Schéma explicatif : 

![](/images/schema_template-3.png)

## Création d’un Template de service (pour les commandes)

Pour créer un Template de service, il faut cliquer sur « Configuration », sur « Services » puis sur « Templates » et enfin sur « Add »

![](/images/template_centreon-1.png)

### Onglet « Informations générales »

Commencer par indiquer un Alias et un Nom pour ce nouveau Template.  
Comme indiqué précédemment, la ligne « Template » permet de choisir un Template afin d’en hériter la configuration.  
La ligne « Check Command » va permettre de choisir la commande de check (par exemple) qui sera utilisée par ce Template et dont les informations seront remontées au serveur Centreon.

![](/images/template_centreon-2.png)

Lors de la création de la commande, la valeur des arguments n’avait pas été indiqué.  
Il est possible de l’indiquer soit dans la partie « arg » du Template soit directement lors de la création d’un hôte.  
Pour la création de cette procédure, les arguments ont été définis dans le Template.   
Il est intéressant de constater que l’on peut également retrouver la description des arguments qui ont été ajoutés à la création de la commande directement dans le Template.

![](/images/template_centreon-3.png)

Indiquer la période de supervision appliqué à ce Template.  
Indiquer le « Max Check Attempts », « Normal Check Interval » et le « Retry Check Interval »

Quand un objet est au statut OK, il est à nouveau vérifié toutes les « Normal Check Interval » minutes.  
Si le statut passe en « WARNING », « CRITICAL » ou « UNKNOWN », il est alors vérifié « Max Check Attempts » fois à un intervalle régulier de « Retry Check Interval » minutes.  
Si le statut de l'objet n'est pas revenu à OK au bout des « Max Check Attempts » essais alors l'intervalle de vérification redevient de « Normal Check Interval » minutes.

![](/images/template_centreon-4.png)

### Onglet « Notifications »

Cet onglet va servir à configurer les informations de notification d’alertes.   
A la ligne « notification enabled » cliquer sur YES  
Indiquer le compte du contact qui recevra les notifications par email  
Cocher les cases correspondantes aux notifications que l’on souhaite recevoir.  
Dans la partie « Notification Period » indiquer 24x7 pour une supervision 24h/24 et 7j/7 puis laisser le reste par défaut.

![](/images/template_centreon-5.png)

### Onglet « Relations »

Cette étape peut se faire soit lors de la création d’un Template de service soit lors de la création d’un Template d’hôte.  
Tout va dépendre de l’ordre de création des Templates. Les deux Templates seront liés l’un à l’autre dans les deux cas.  
Cet onglet va permettre de lier ce Template directement avec un Template d’hôte.

![](/images/template_centreon-6.png)

Il est également possible de configurer les « Traps SNMP » depuis cet onglet.  
Ces « Traps » ne seront pas abordées actuellement dans cette documentation.

![](/images/template_centreon-7.png)

### Onglet « Data PROCESSING »

Cet onglet permet de configurer les informations nécessaires au traitement des données.  
Il ne sera pas détaillé dans cette procédure.

### Onglet « Extended Info »

Cet onglet permet de configurer les informations nécessaires à la création de graphiques.  
Il ne sera pas détaillé dans cette procédure.

## Création d’un Template d’hôte

Pour créer un Template d’hôte, il faut cliquer sur « Configuration », sur « hôte » puis sur « Templates » et enfin sur « Add »

![](/images/template_centreon-8.png)

### Onglet « Host Configuration »

Pour commencer, indiquer un Nom et un Alias pour ce nouveau Template.  
En cas de check à l’aide du protocole SNMP, indiquer le nom de la communauté utilisée et la version du protocole.  
La ligne « Template » permet de choisir un Template afin d’en hériter la configuration, ajoutant des options supplémentaires à chacun des hôtes qui sera affilié à ce Template.

![](/images/template_centreon-9.png)

La ligne « Check Command » va permettre de choisir une commande de check (par exemple) qui sera utilisée par ce Template et dont les informations seront remontées au serveur Centreon. Dans le cas de cette procédure, nous n’allons pas utiliser cette option mais plutôt lier des Templates de service à ce Template d’hôte. Permettant à l’hôte d’hériter des commandes de chaque Template de service associé.

![](/images/template_centreon-10.png)

Indiquer la période de supervision appliqué à ce Template.  
Indiquer le « Max Check Attempts », « Normal Check Interval » et le « Retry Check Interval »

Pour rappel, quand un objet est au statut OK, il est à nouveau vérifié toutes les « Normal Check Interval » minutes.  
Si le statut passe en « WARNING », « CRITICAL » ou « UNKNOWN », il est alors vérifié « Max Check Attempts » fois à un intervalle régulier de « Retry Check Interval » minutes.  
Si le statut de l'objet n'est pas revenu à OK au bout des « Max Check Attempts » essais alors l'intervalle de vérification redevient de « Normal Check Interval » minutes.

Ensuite mettre « Active check » sur oui et « Passive check » sur non.

![](/images/template_centreon-11.png)

### Onglet « Notifications »

Cet onglet va servir à configurer les informations de notification d’alertes.   
A la ligne « notification enabled » cliquer sur YES  
Indiquer le compte du contact qui recevra les notifications par email  
Cocher les cases correspondantes aux notifications que l’on souhaite recevoir. Pour cette procédure, tout est coaché sur « aucune ».  
Dans la partie « Notification Period » indiquer 24x7 pour une supervision 24h/24 et 7j/7 puis laisser le reste par défaut.

![](/images/template_centreon-12.png)

### Onglet « Relations »

Cet onglet va permettre de lier ce Template directement avec un ou plusieurs Templates de service.

![](/images/template_centreon-13.png)

![](/images/template_centreon-14.png)

Il est également possible de configurer la hiérarchie « Parent » depuis cet onglet.  
Une fois les informations configurées, cliquer sur sauvegarder

### Onglet « Data PROCESSING »

Cet onglet permet de configurer les informations nécessaires au traitement des données.  
Il ne sera pas détaillé dans cette procédure.

### Onglet « Extended Info »

Cet onglet permet de configurer les informations nécessaires à la création de graphiques.  
Il ne sera pas détaillé dans cette procédure.