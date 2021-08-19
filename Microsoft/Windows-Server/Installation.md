---
title: Installation de Windows serveur
description: 
published: true
date: 2021-08-09T19:51:49.773Z
tags: 
editor: markdown
dateCreated: 2021-05-24T10:36:17.006Z
---

# Installation de Windows serveur 2019

# Prérequis

-   Un logiciel de virtualisation : [https://www.virtualbox.org/wiki/Downloads](https://www.virtualbox.org/wiki/Downloads)
-   L'ISO de Windows Serveur : [https://www.microsoft.com/fr-fr/evalcenter/evaluate-windows-server](https://www.microsoft.com/fr-fr/evalcenter/evaluate-windows-server)

# Création de la machine virtuelle

![](/images/conf_windows_serveur_2019.png)

# Installation de Windows Serveur 2019

Lancer la machine virtuelle, celle-ci démarrera automatiquement sur l'ISO de Windows serveur 2019

![](/images/install.png)

-   Cliquez sur **suivant** 

![](/images/install2.png)

-   Puis sur **Installer maintenant**

![](/images/install3.png)

Plusieurs versions nous sont proposées : 

-   Windows Server 2019 Standard :

Windows Server Standard est conçu pour les petites et moyennes entreprises sans interface graphique.

-   Windows Server 2019 Standard (expérience de bureau) :

Windows Server Standard est conçu pour les petites et moyennes entreprises avec interface graphique.

-   Windows Server 2019 Datacenter : Windows Server Datacenter est conçu pour les grandes entreprises sans interface graphique.
-   Windows Server 2019 Datacenter (expérience de bureau) : Windows Server Datacenter est conçu pour les grandes entreprises avec interface graphique.

-   Une fois votre choix fait, cliquer sur **suivant**

![](/images/install4.png)

-   Une fois le contrat d'utilisation lu et accepter, cliquer sur **Suivant**

![](/images/install6.png)

-   Choisir le type d’installation : **Personnalisé : installer uniquement Windows** (avancé)

![](/images/install7.png)

-   Une fois votre disque sélectionné, cliquez sur **Suivant**

![](/images/install8.png)

-   L'installation de Windows Serveur commence, il ne vous reste plus qu'à patienté.

![](/images/install9.png)

Après l'installation le serveur redémarre, et vous propose de définir le mot de passe du compte Administrateur.

-   Une fois le mot de passe, défini cliquer sur **Terminer.**

# **Configuration post-installation**

Une fois connecté,  le Gestionnaire de serveur se lance automatiquement.

![](/images/install10.png)

-   Cliquez sur le nom de l’ordinateur afin d’ouvrir les propriétés système. WIN-XX
-   Dans la fenêtre **Propriétés système :**
    -   Cliquez sur **Modifier** et changer le nom du serveur (Renseigner le nom que vous souhaitez), nous allons choisir wikitech
    -   Cliquez  sur **OK** puis sur **Fermer**.
    -   **Redémarrez le serveur** pour rendre  les modifications actives.

Maintenant nous allons passer à la configuration réseau du serveur.

-   Effectuez le raccourci clavier Windows + r, une fenêtre s'ouvre.
    -   Taper la commande ncpa.cpl, puis cliquer sur ok.
    -   Clic droit sur la carte réseau, puis sur **Propriété**
    -   Double clic sur **Protocole Internet version 4 (TCP/IPv4)**

![](/images/conf_ip.png)

Voilà. Vous venez d’installer Windows Server 2019.