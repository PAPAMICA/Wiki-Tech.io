---
title: Windows-Server - GPO
description: Comprendre et créer des GPO
published: true
date: 2021-06-14T07:58:22.703Z
tags: 
editor: markdown
dateCreated: 2021-05-24T10:36:14.840Z
---

# Qu’est-ce qu’une GPO ?

Les GPO (Group Policy Object ou Stratégies de groupe) sont des **règles que l’on peut appliquer à des utilisateurs, groupes ou OU (Unité d’Organisation) présent dans un annuaire Active Directory**. Cela suppose donc qu’il y ait un domaine de configuré sur notre serveur.

## Ouvrir le gestionnaire de stratégie de groupe

Sur le serveur, lancez le **Gestionnaire de serveur.** Allez ensuite dans Outils > **Gestion des stratégies de groupe**.

![](https://i1.wp.com/labo-tech.fr/wp-content/uploads/2018/11/gp1.1-920x455.png?resize=920%2C455)

## Appliquer les GPO

Afin d’appliquer toutes vos GPO, vous devez redémarrer le poste en question ou directement utiliser la commande suivante : “**gpupdate /force**” dans l’invite de commande.

![](https://i1.wp.com/labo-tech.fr/wp-content/uploads/2018/11/gp1.9.png?resize=446%2C217)

---

# Mapper un lecteur réseau

## Pré-requis

-   [Un dossier partagé sur le réseau](https://labo-tech.fr/base-de-connaissance/comment-partager-un-dossier-sur-le-reseau/) 

## Création de la GPO

Dans le **Gestionnaire de stratégie de groupe**, dépliez les onglets jusqu’à avoir l’affichage suivant, faites un clique droit sur votre domaine et choisissez “**Créer un objet GPO dans ce domaine, et le lier ici...**“. 

![](https://i1.wp.com/labo-tech.fr/wp-content/uploads/2018/11/gp1.2.png?resize=754%2C531)

Donnez un nom à votre GPO puis faîtes “OK”.

![](https://i2.wp.com/labo-tech.fr/wp-content/uploads/2018/11/gp1.3.png?resize=386%2C182)

Une nouvelle GPO doit alors apparaître en dessous de “Default Domain Policy” avec le nom que vous lui avez donné.

Remarque: Si vous avez beaucoup de GPO, vous pouvez aussi les ranger dans des dossiers (OU) : Clic droit, “Nouvelle unité d’organisation”, puis vous créer votre GPO dedans.

Une fois celle-ci créée, faîtes Clic droit dessus puis “Modifier”.

![](https://i1.wp.com/labo-tech.fr/wp-content/uploads/2018/11/gp1.4.png?resize=753%2C529)

Allez dans **Configuration utilisateur > Préférences > Paramètres Windows > Mappages de lecteurs.**

La partie de droite de la fenêtre affiche alors en gros “Mappages de lecteurs”. Faîtes Clic droit dans cette partie puis choisissez “Nouveau” et enfin “Lecteur Mappé”.

![](https://i1.wp.com/labo-tech.fr/wp-content/uploads/2018/11/gp1.5-920x557.png?resize=920%2C557)

Une nouvelle fenêtre s’ouvre et c’est ici que tout se joue…

Parmi, les actions, il y en a 4. Je vous conseille de choisir “**Mettre à jour**“. Pour **l’emplacement, il faut indiquer le chemin réseau** de voter dossier.

**Cocher la case “Reconnecter”** et donner le **nom que vous souhaitez pour le libéllé**.

Vous avez ensuite la **possibilité de choisir lettre qu’aura le lecteur** (les options parlent d’elle mêmes).

Enfin **cochez “Afficher ce lecteur” et “Afficher tous les lecteurs”**.

![](https://i0.wp.com/labo-tech.fr/wp-content/uploads/2018/11/gp1.6.png?resize=399%2C458)

Appliquez les paramètres puis faîtes “OK”. Vous verrez alors qu’une nouvelle ligne s’est ajoutée, dans la partie “Mappages de lecteurs”.

Vous pouvez fermer cette fenêtre et revenir à celle listant vos GPO.

Dans la partie de droite, en bas, **vous pouvez choisir sur qui cette GPO va s’appliquer (utilisateurs, groupes, OU)**. Vous n’avez qu’à cliquer sur ajouter puis entrer les noms souhaités et faire “OK”.

## Résultat

![](https://i2.wp.com/labo-tech.fr/wp-content/uploads/2018/11/gp1.10.png?resize=786%2C592)

---

# Déployer une imprimante

## Pré-requis

-   [Un serveur d’impression](https://labo-tech.fr/base-de-connaissance/comment-installer-un-serveur-dimpression/)
-   Une imprimante configurée sur le serveur

## Création de la GPO

Ouvrez le **Gestionnaire d’impression** depuis le **Gestionnaire de serveur**.

![](https://i1.wp.com/labo-tech.fr/wp-content/uploads/2018/11/gp2.1-920x475.png?resize=920%2C475)

Déroulez le menu jusqu’à trouver les imprimants, choisissez votre imprimante et cliquer sur “**Déployer avec la stratégie de groupe**“.

![](https://i2.wp.com/labo-tech.fr/wp-content/uploads/2018/11/gp2.2-920x701.png?resize=920%2C701)

Cliquez sur **Parcourir** afin de sélectionner une GPO.

![](https://i1.wp.com/labo-tech.fr/wp-content/uploads/2018/11/gp2.3.png?resize=698%2C426)

Sélectionnez une GPO ou cliquez sur l’icon afin d’en créer une nouvelle.

![](https://i0.wp.com/labo-tech.fr/wp-content/uploads/2018/11/gp2.4.png?resize=478%2C356)

Nommez et sélectionnez la GPO puis cliquez sur OK.

![](https://i0.wp.com/labo-tech.fr/wp-content/uploads/2018/11/gp2.5.png?resize=481%2C358)

Choisissez comment vous voulez déployer votre imprimante : par ordinateur, par utilisateur ou les deux et cliquez sur Ajouter.

![](https://i2.wp.com/labo-tech.fr/wp-content/uploads/2018/11/gp2.6.png?resize=698%2C423)

Vous retrouvez un récapitulatif de vos actions, si tout est bon, cliquez sur OK

![](https://i2.wp.com/labo-tech.fr/wp-content/uploads/2018/11/gp2.7.png?resize=697%2C424)

Vous pouvez voir les imprimantes que vous avez déployé directement dans le menu **Imprimantes déployées**.

![](https://i2.wp.com/labo-tech.fr/wp-content/uploads/2018/11/gp2.8-920x700.png?resize=920%2C700)

## Résultat

![](https://i0.wp.com/labo-tech.fr/wp-content/uploads/2018/11/gp2.10.png?resize=784%2C591)

---

# Déployer un logiciel

## Pré-requis

-   Le .msi d’un logiciel

## Création de la GPO

> Pas encore fini