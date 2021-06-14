---
title: Vues personnalisées
description: Les vues personnalisées
published: true
date: 2021-06-14T08:14:20.230Z
tags: 
editor: markdown
dateCreated: 2021-05-24T10:37:22.673Z
---

![Centreon | Supervision Informatique : Infrastuctures Hybrides, Cloud, AIOps](https://static.centreon.com/wp-content/uploads/2019/10/Centreon_Logo_RVB_HD_Cut.png?x55598)

# Configuration des vues personnalisées

Lors que la configuration de Centreon est effectuée et que le serveur est prêt à l’emploi, il est possible d’effectuer certaines configurations afin de le personnaliser.  
Par exemple il est possible d’ajouter des vues personnalisées et précises directement dans la section « Home » puis « Custom Views »

## Installation des « Widgets »

Avant de commencer la première chose à faire est d’installer les différents « Widgets » dont nous aurons besoin.  
Ils sont disponibles dans « Administration » puis « Extensions » et en fin dans « Widgets ».  
Une liste sera visible avec le titre, la description, la version l’auteur et action (qui correspond en réalité au statut activé ou désactivé).   
Les « Widgets » ayant pour icône ce symbole un écrou ne sont pas installés tandis que ceux ayant pour icône une croix rouge sont installés.  
Dans cette procédure, nous allons installer les « Widgets » suivant : 

-   Graph Monitoring
-   Grid-map
-   Host Monitoring
-   Hostgroup Monitoring
-   Service Monitoring
-   Servicegroup Monitoring
-   Tactical Overview

Pour ce faire il suffit de cliquer sur l’icône en forme d'écrou.  
Un message de confirmation va apparaitre et il suffira de valider par « OK »

![](/images/vue_perso_centreon-3.png)

Les « Widgets » sont maintenant installés

![](/images/vue_perso_centreon-4.png)

### Création d’une vue personnalisée

Maintenant que les « Widgets » sont installés nous allons pouvoir créer des vues personnalisées afin d’avoir un bref aperçu de la supervision directement depuis l’interface graphique de Centreon.   
Il est possible d’avoir plusieurs informations réunies dans une seule vue ou alors une seule information par vue. Tout va dépendre de la configuration de la vue.  
Cliquer sur « Home » puis sur « Custom Views » et en fin sur « + Add view »

![](/images/vue_perso_centreon-5.png)

Un pop-up va apparaitre afin de donner des critères à cette nouvelle vue.

![](/images/vue_perso_centreon-6.png)

La section « Layout » permet de définir le nombre de colonnes qui seront configurées dans la vue.  
Par exemple SI 1 colonne est choisie, toute la largeur de la page web sera utilisée pour afficher une information. Si on souhaite ajouter d’autres informations à cette vue, elles seront dans ce cas les unes en dessous des autres.  
Dans le cas où 2 ou 3 colonnes seraient configurées, les informations seront alors les unes à côté des autres pour une meilleure visibilité.

A titre d’exemple pour cette procédure, 2 colonnes seront choisies.  
Remplir les informations comme ceci : 

![](/images/vue_perso_centreon-7.png)

Cocher la case « public » si les utilisateurs qui ne sont pas administrateur ont besoin d’avoir accès à cette vue.  
Cliquer sur « submit »  
La vue est alors créée mais ne contient pour l’instant aucune information.

![](/images/vue_perso_centreon-8.png)

Pour ajouter des informations, cliquer sur « + Add widget ». Un nouveau pop-up va alors apparaitre.  
Indiquer un nom et sélectionner le « widget » à utiliser. Pour cet exemple, « host monitoring » sera utilisé.

![](/images/vue_perso_centreon-9.png)

Cliquer sur « submit »  
Une vue va alors apparaitre mais n’est pour l’instant pas personnalisée.

![](/images/vue_perso_centreon-10.png)

Cliquer sur la clé en haut à droite pour faire apparaitre la page de configuration.  
Sélectionner le « hostgroup » qui sera utilisé. Dans notre cas nous allons afficher tous les serveurs Windows donc le groupe choisi sera « Serveurs\_Win ».   
Modifier le nombre dans la section « results » afin d’afficher autant d’hôtes que souhaité.

![](/images/vue_perso_centreon-11.png)

Dans la partie basse du pop-up, indiquer l’intervalle de temps en seconde pour le rafraichissement automatique de la vue.

![](/images/vue_perso_centreon-12.png)

Cliquer sur « apply »  
La vue est maintenant configurée et le « widget » est visible.  
Répéter l’opération afin d’ajouter un autre « widget » à cette vue. Il sera visible dans la deuxième colonne.

![](/images/vue_perso_centreon-13.png)