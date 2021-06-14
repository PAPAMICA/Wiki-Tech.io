---
title: Déployer la configuration
description: Déploiement de la configuration de Centreon
published: true
date: 2021-06-14T08:14:12.765Z
tags: 
editor: markdown
dateCreated: 2021-05-24T10:37:05.065Z
---

![Centreon | Supervision Informatique : Infrastuctures Hybrides, Cloud, AIOps](https://static.centreon.com/wp-content/uploads/2019/10/Centreon_Logo_RVB_HD_Cut.png?x55598)

# Déployer la configuration

## Démarrer la supervision

-   Générer les fichiers de configuration : Génère les fichiers de configuration de l’ordonnanceur dans un répertoire temporaire. Cette configuration est générée à partir des objets configurés via l’interface web
-   Lancer le débogage du moteur de supervision (-v) : Permet à l’ordonnanceur de vérifier la configuration générée
-   Déplacer les fichiers générés : Déplace les fichiers de configuration du répertoire temporaire vers le répertoire de l’ordonnanceur
-   Redémarrer l’ordonnanceur : Redémarre l’ordonnanceur afin d’appliquer les nouveaux fichiers de configuration
-   Commande exécutée post-génération : Exécute la commande post-génération paramétrée au niveau de la configuration de l’ordonnanceur

## Première étape

Pour démarrer la supervision il faut se rendre sur l‘interface graphique.  
Dans le menu Configuration puis « pollers », tout laisser par défaut et cliquer sur exporter.

![](/images/deploiement_centreon-1.png)

Une nouvelle page apparait.

![](/images/deploiement_centreon-2.png)

Dans la partie « Collecteurs », sélectionner le serveur 

![](/images/deploiement_centreon-3.png)

Vérifier que les cases « Générer les fichiers de configuration » et « Lancer le débogage du moteur de supervision (-v) » soient bien cochées. Vérifier que les cases « Déplacer les fichiers générés » ainsi que « Redémarrer l’ordonnanceur » soient bien décochées.  
Cliquer à nouveau sur « Exporter ».

Si tout s’est bien passé, les messages « ok » apparaissent. Dans le cas contraire il faudra corriger les erreurs

![](/images/deploiement_centreon-4.png)

## Deuxième étape

Décochez les cases « Générer les fichiers de configuration » et « Lancer le débogage du moteur de supervision (-v) »  
Cochez les cases « Déplacer les fichiers générés » ainsi que « Redémarrer l’ordonnanceur »

![](/images/deploiement_centreon-5.png)

Cliquez sur « Exporter » à nouveau  
Si tout s’est bien passé, les messages « ok » apparaissent à nouveau. Dans le cas contraire il faudra corriger les erreurs

![](/images/deploiement_centreon-6.png)

Chose importante, il est IMPÉRATIF d’effectuer la « première étape » ainsi que la « deuxième étape » après chaque modification faite à la configuration de Centreon pour que celle-ci soit prise en compte, sans quoi même si des modifications ont été apportées elles ne seront pas utilisées pour la supervision.