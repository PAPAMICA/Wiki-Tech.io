---
title: Comptes et Groupes
description: 
published: true
date: 2021-06-14T08:12:48.309Z
tags: 
editor: markdown
dateCreated: 2021-05-24T10:37:07.237Z
---

![Centreon | Supervision Informatique : Infrastuctures Hybrides, Cloud, AIOps](https://static.centreon.com/wp-content/uploads/2019/10/Centreon_Logo_RVB_HD_Cut.png?x55598)

# Comptes et Groupes d’Utilisateurs dans Centreon

## Création de comptes pour les utilisateurs

Afin d’ajouter des comptes utilisateurs il faut se rendre dans la section « configuration » puis « users »  
Cliquer sur « add »

![](/images/users_centreon-1.png)

Remplir les différents champs nécessaires puis cliquer sur « save »

![](/images/users_centreon-2.png)

## Création de groupes d’utilisateurs

Afin d’ajouter des groupes de comptes utilisateurs il faut se rendre dans la section « configuration » puis « users » et pour finir « contact groups ».  
Cliquer sur « add »

![](/images/users_centreon-3.png)

Remplir les différents champs nécessaires puis cliquer sur « save »

![](/images/users_centreon-4.png)

## Configuration des ACL

### Création de groupes d’accès

Avant de commencer à configurer les ACL il va falloir créer des groupes d’accès.  
A titre d’exemple pour cette procédure les groupes suivants seront créés : 

-   Admin Réseau
-   Webmaster
-   DSI

Pour créer les groupes, cliquer sur « administration » puis « ACL », choisir « Access Groups » et cliquer sur « add »

![](/images/acl_centreon-4.png)

Remplir les champs « Group Name » et « Alias » puis cliquer sur « save »

![](/images/acl_centreon-2.png)

### Gestion des droits des groupes d’accès

Maintenant que les groupes d’accès sont créés, il va falloir leur affecter des autorisations.

#### Les pages accessibles

Cliquer sur « administration » puis « ACL », choisir « Menu Access » et cliquer sur « add »

![](/images/acl_centreon-3.png)

Remplir les champs « ACL Definition » et « Alias ».  
Mettre le statut sur « Enabled » puis dans la partie « Linked Groups » choisir le groupe qui sera lié à ce « menu d’autorisations »

![](/images/acl_centreon-5.png)

Dans la section « Accessibles pages » il est possible de choisir en détail ce qui pourra être consulté ou non.  
Pour cet exemple, le groupe choisi étant « Admin Réseau » nous allons lui accorder un accès total.  
Pour cela il suffit de cocher les cases de droite sans forcément dérouler les menus qui sont listés.  
Penser également à mettre un commentaire afin de savoir à quoi correspond le menu

![](/images/acl_centreon-6.png)

Autre exemple avec le groupe « Webmaster » qui n’aura pas un full access.  
Contrairement au menu admin réseau, dans la section « Accessible Pages » nous n’allons pas tout cocher pour tout autoriser.  
Il est conseillé pour des raisons de facilité de gestions en cas de changement d’autorisations, de créer un menu par catégorie permettant un accès (home, monitoring, etc.)  
Procéder à la création du menu de la même manière que précédemment.  
Remplir à nouveau les champs « ACL Definition » et « Alias » puis mettre le statut sur « Enabled ».  
Dans la partie « Linked Groups » choisir le groupe Webmaster et cliquer sur « add ».

![](/images/acl_centreon-7.png)

Dans la section « Accessible Pages », dérouler la catégorie concernée par le menu et cocher les cases correspondantes aux différentes autorisations. Cliquer sur « save »

![](/images/acl_centreon-8.png)

Reproduire cette configuration pour chacune des catégories souhaitées.

![](/images/acl_centreon-9.png)

#### Les ressources accessibles

La configuration des différentes pages accessibles étant faite, il reste maintenant la gestion des ressources accessibles.  
Les groupes d'accès « DSI » et « admin réseau » ont accès à toutes les ressources.  
Il est donc possible d'utiliser le groupe de ressource « All Resources » déjà existant pour y ajouter les deux groupes.

![](/images/acl_centreon-10.png)

Toujours à titre d’exemple pour cette procédure, le groupe Webmaster n’aurai pas un accès à l’intégralité des ressources.  
Cliquer sur « Configuration » puis « hosts » puis « Categories » et enfin sur « add »

![](/images/acl_centreon-11.png)

Remplir les différents champs et ajouter soit des groupes d’hôtes soit des hôtes à la liste.

![](/images/acl_centreon-12.png)

Cliquer sur « save »  
Effectuer la même opération pour les services.  
Cliquer sur « Configuration » puis « Services » puis « Categories » et enfin sur « add »  
Remplir également les différents champs et sélectionner les services associés

![](/images/acl_centreon-13.png)

Cliquer sur « save »

Les deux catégories étant créées, il faut maintenant cliquer sur « administration » puis « ACL », choisir « Ressources Access » et cliquer sur « add » afin de créer une nouvelle ressource pour le groupe Webmaster.

![](/images/acl_centreon-14.png)

Remplir les champs « Access list name » et « Description » puis mettre le statut sur « Enabled ».  
Dans la partie « Linked Groups » choisir le groupe Webmaster et cliquer sur « add ».

![](/images/acl_centreon-15.png)

-   Cliquer en suite sur l’onglet « Hosts Resources »

Cet onglet va permettre de définir les Hôtes et les groupes d’hôtes auxquels aura accès le groupe Webmaster mais également ceux qui seront exclus des ressources visibles et disponibles.  
Sélectionner ceux disponibles et correspondant à la catégorie précédemment créée puis cliquer sur « add »

![](/images/acl_centreon-16.png)

-   Cliquer en suite sur l’onglet « Services Resources »

Effectuer la configuration de la même manière que pour l’onglet « Hosts Resources ».

-   L’onglet « Filters » permet de choisir le ou les serveurs centreon depuis lesquels les ressources seront accessibles.

####  Gestion des actions

En plus des pages affichées et des ressources disponibles, il est possible de configurer les actions réalisables pour les différents groupes créés.  
Cliquer sur « administration » puis « ACL », choisir « Action Access »   
Pour le groupe DSI, aucune action n’est prévue mais il sera nécessaire d’activer la case « top counter »  
Remplir les différents champs de description et cocher la case.

![](/images/display.png)

Le groupe Webmaster pourra par exemple être associé au groupe d’action « Simple User », limitant ses actions sur les hôtes.

![](/images/acl_centreon-17.png)

A titre d’exemple, le groupe « Admins Réseau » aura quant à lui plus de droits.  
Cliquer sur « administration » puis « ACL », choisir « Action Access » et cliquer sur « add »

![](/images/acl_centreon-18.png)

Remplir les différents champs d’information (titre, alias etc.) et sélectionner les droits applicables au groupe.

![](/images/acl_centreon-19.png)

Cliquer sur « save »

Toutes ces opérations pourront être réaliser à nouveau chaque fois que nécessaire en fonction des besoins et des groupes sur lesquels appliquer des ACL.