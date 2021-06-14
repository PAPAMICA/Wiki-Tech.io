---
title: SNMP
description: 
published: true
date: 2021-06-14T08:11:14.958Z
tags: 
editor: markdown
dateCreated: 2021-05-24T10:37:29.105Z
---

## **Activation du SNMP sur le NAS**

Avant de commencer, il est nécessaire d’activer le SNMP sur le périphérique que vous souhaitez superviser, dans notre cas un NAS Synology.

L’activation et la configuration du SNMP sur un NAS Synology est très simple :

Connectez-vous sur votre NAS Synology.

Dans le panneau de configuration en mode avancé, cliquez sur "**Terminal & SNMP"** .

![](https://www.tech2tech.fr/wp-content/uploads/2020/01/nas1-1024x586.png)

Dans l’onglet **SNMP**, activez le protocole et rentrez votre **communauté**. (A vous de choisir, il faudra cependant vous en souvenir)

![](https://www.tech2tech.fr/wp-content/uploads/2020/01/nas2-1024x716.png)

Vous pouvez aussi remplir les champs de renseignements.

Pour finir, n’oubliez pas d’**appliquer les paramètres** !

## **Ajout du modèle dans Zabbix**

Afin de pouvoir comprendre les informations récoltées par Zabbix en SNMP, nous devons utiliser un **modèle** qui comprend les **MIB** (MIB signifie Management Information Base. Les fichiers MIB vous permettent d’utiliser la représentation textuelle de l’OID (identificateur d’objet).

Vu que je suis un gars sympa (oui oui !), je vous partage quelques-uns de mes templates directement sur mon **Github**, d’autres viendront avec le temps.

Lien vers mon Github Tempate : [https://github.com/PAPAMICA/Templates](https://github.com/PAPAMICA/Templates)

Connectez-vous sur votre serveur **Zabbix**.

Téléchargez le **modèle SNMP** pour NAS Synology sur mon Github.

Rendez-vous dans la configuration des modèles.

![](https://www.tech2tech.fr/wp-content/uploads/2020/01/nas-4-1024x113.png)

Cliquez sur « **Importer**« .

Importez le modèle précédemment téléchargé dans Zabbix.

Votre modèle est maintenant disponible dans Zabbix, passons à la configuration de la supervision du NAS !

## **Configuration de l’hôte sur Zabbix**

Maintenant que votre NAS Synology dispose d’un accès en SNMP et que nous disposons de notre modèle, attaquons-nous à la configuration de l’hôte pour votre NAS :

Connectez-vous sur votre serveur **Zabbix**.

Rendez-vous dans la configuration des hôtes.

![](https://www.tech2tech.fr/wp-content/uploads/2020/01/nas3-1024x113.png)

Cliquez sur « **Créer un hôte**« .

Configurer votre hôte de la manière suivante :

![](https://www.tech2tech.fr/wp-content/uploads/2020/01/nas5-1024x734.png)

Sélectionnez le **modèle** précédemment importé.

![](https://www.tech2tech.fr/wp-content/uploads/2020/01/nas6-1024x252.png)

Configurez une **macro** avec votre **communauté** configurée un peu plus tôt.

![](https://www.tech2tech.fr/wp-content/uploads/2020/01/nas7-708x129.png)

Cliquez sur « **Ajouter**« .

Votre NAS est désormais supervisé avec votre serveur Zabbix ! Vous pourrez retrouver toutes les informations dans la catégorie « **Dernières données**« . (Prévoyez 1 à 2 heures pour que toutes les informations remontent dans Zabbix)