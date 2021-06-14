---
title: Présentation de Docker
description: Qu'est-ce que Docker et à quoi ça sert ?
published: true
date: 2021-06-14T07:10:07.567Z
tags: 
editor: markdown
dateCreated: 2021-05-24T10:34:29.710Z
---

![](https://i1.wp.com/labo-tech.fr/wp-content/uploads/2019/11/docker.png?resize=920%2C316&ssl=1)

# Introduction

Docker est un projet Open-Source , distribué en tant que tel depuis mars 2013. A l’heure actuelle, Docker c’est plus de 13 500 forks et 1 700 contributeurs sur leur page Github. Autant dire qu’en quelques années, le projet a gagné en popularité jusqu’à même son intégration native sur certains systèmes, par exemple, les NAS de la marque Synology.

Maintenant que l’historique (plutôt récent) est fait, nous allons pouvoir aborder la question de l’intérêt de Docker.

# Les Conteneurs

Avant de parler de Docker, nous devons définir la notion de Conteneurs.

Un conteneur, en informatique, est un ensemble de processus qui sont isolés du reste du système hôte. Le conteneur s’exécute à partir d’une image contenant tous les fichiers nécessaires à son exécution (dépendances par exemple). Ainsi, de cette manière, le conteneur propose une solution portable permettant une exécution d’application fiable entre plusieurs machines. De cette manière, en transférant le code et son environnement, la situation du code se lançant à merveille sur notre ordinateur personnel, mais qui ne fonctionne plus sur l’ordinateur d’un autre individu n’a plus lieu d’être.

L’un des autres aspects intéressants des conteneurs est leur isolation. En effet, les conteneurs sont isolés via l’OS, ils partagent le même noyau, mais sont complètement indépendants les uns des autres. Via différents outils les conteneurs vont permettre leur isolation, allocation des ressources… :

Les Namespaces vont avoir pour but d’isoler les processus, pour que chaque conteneur n’ait accès qu’à leurs process. Cette isolation va concerner aussi le réseau, car le conteneur va posséder sa propre interface réseau, ainsi que les mounts (pour isoler chaque filesystem). Cette isolation via namespace concerne donc chaque ressource qui va être partagée entre le système hôte et le conteneur et permet son isolation. Les groupes de contrôles vont avoir pour but de gérer et contrôler les accès aux ressources, mais aussi de mesurer l’empreinte en consommation des conteneurs présents.

Comme vu juste au-dessus, les conteneurs n’embarquent pas d’OS à proprement parlé, ce qui fait qu’ils sont très légers, là ou une Machine virtuelle classique va virtualiser l’OS, ses pilotes, des périphériques… et enfin l’application, le conteneur lui va se contenter du minimum pour fonctionner en se basant sur les ressources déjà existantes de l’OS hôte.

Ces Vm “light” ouvrent de nouvelles portes et donnent un nouveau regard sur la virtualisation en entreprise. Prenons par exemple le cas d’une entreprise développant des logiciels. Pour lancer une phase de test de son produit, elle doit mettre en place plusieurs environnements qui pourraient montrer des bugs inconnus. A ce moment-là que va-t-elle choisir ? Utiliser 10-100-1000 machines virtuelles ? Cela serait extrêmement coûteux et très gourmand en ressources, c’est dans ce genre de situation que les conteneurs semblent en tout point disposés à réaliser ce genre de tests de manière bien plus optimale.

# Docker dans tout ça ?

La philosophie de Docker est “Build, Ship, and Run Any App, Anywhere”, cette phrase résume à elle seule tout l’intérêt et le fonctionnement de Docker. Via cette technologie , vous allez pouvoir développer votre application dans un conteneur Docker en utilisant la distribution et les outils de votre choix. Par la suite, vous allez pouvoir envoyer votre conteneur où vous le souhaitez que ce soit une personne ou une entité physique ou bien un cloud. Par la suite, vous allez pouvoir lancer votre application avec une certaine liberté d’action (gestion jusqu’à 1000 nœuds, update rapide sans latence…).

Docker est donc ce que l’on va appeler un “Hyperviseur” de conteneurs, grâce à lui vous allez pouvoir gérer et déployer facilement vos conteneurs, mais il ne s’arrête pas là.

L’architecture de Docker est divisée en trois parties :

-   Docker\_Client
-   Docker\_Host
-   Registry

Le Docker\_Host contient le Daemon (processus) de Docker. C’est via celui-ci que nous allons pouvoir instancier un conteneur à partir d’une image système (de type Ubuntu par exemple).

Docker\_Client est une application en lignes de commande qui vont permettre une communication entre l’utilisateur et le daemon présent dans le Docker\_Host.

Registry est un serveur permettant l’envoi, le stockage, et la récupération de vos images Docker stockées dans un registre privé. Nous y trouvons aussi le Docker hub, qui met à disposition des images prêtes à l’emploi, qu’elles soient vierges ou pour une utilisation spécifique (une fresh install d’Ubuntu ou le même système avec un serveur Nginx préconfiguré) dans un registre public.