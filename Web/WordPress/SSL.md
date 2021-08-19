---
title: Activer le SSL sur un site WordPress
description: Mettre en place le petit cadenas du HTTPS sous WordPress !
published: true
date: 2021-06-15T12:02:26.958Z
tags: wordpress, site, ssl, https
editor: markdown
dateCreated: 2021-06-15T11:59:37.230Z
---

![How to set-up free SSL Let&#39;s Encrypt using WHM | Search Meow Marketing](https://searchmeowmarketing.com/wp-content/uploads/2019/04/ssl-encryption.png)

# Présentation

HTTPS signifie **Hyper Text Transfer Protocol Secure**. Autrement dit, un site HTTPS est un site internet comme les autres (il fonctionne à l’aide du protocole client – serveur HTTP), à la différence près qu’il est considéré comme sûr. Le « S » de Secure indique que le site a été sécurisé au moyen d’un **certificat SSL**.

Développé par l’entreprise américaine Netscape dans les années 1990, SSL signifie quant à lui **Secure Socket Layer**. Traduisez par Couches de Prises Sécurisées.

Ça ne veut pas dire grand-chose comme ça mais pour faire simple, un certificat SSL établit une **connexion sécurisée entre deux machines** (ici entre votre ordinateur et un serveur distant) à l’aide d’une **clé chiffrée**.

![http https](https://www.digitom.fr/wp-content/uploads/2019/01/SSL-Schema.png)

Les informations qui circulaient avant librement dans les « tuyaux » d’internet (votre nom, votre adresse, votre numéro de carte bancaire…) sont désormais chiffrées donc illisibles pour les pirates informatiques avec le certificat SSL.

> *Par exemple :*
> 
> ***Sans SSL** : n° de carte bancaire : 1568 4554 7878 9652*
> 
> ***Avec SSL** : n° de carte bancaire : efe564fef$fef\*fùvr$rvz86zfe*

Un certificat SSL **s’installe sur le serveur** comme vous pouvez le voir dans la petite infographie ci-dessus.

Notez également qu’un **nouveau type de certificat SSL**, plus perfectionné, a fait son apparition ces dernières années : le **TLS** (Transport Layer Security).

Pour aller encore plus loin, on peut différencier **3 types de certificats SSL**, du moins sécurisé au plus sécurisé (et donc du moins cher au plus cher)

1.  Le premier type est un certificat de **validation de nom de domaine.** Outre le principe de base de chiffrement des informations, le certificat valide que tel nom de domaine (par ex wiki-tech.io) appartient à telle personne (par exemple Mickael Asseline). Cependant, aucune preuve de l’existence de la personne n’est demandée (par exemple enregistrement, ou SIRET)
2.  Le second type est un certificat de **validation de l’organisation**. Il est semblable au premier, sauf qu’il s’assure de l’existence de l’entreprise qui possède le nom de domaine.
3.  Le dernier type de certificat est dit « de **validation étendue**». Ici, de nombreux facteurs vont être analysés afin d’assurer à 100% que le site est bien celui de son entreprise. Ce type de certificat se traduit par la présence du nom de l’entreprise après le cadenas du HTTPS comme vous pouvez le voir ci-dessous avec l’exemple de la FNAC

![SSL FNAC](https://www.digitom.fr/wp-content/uploads/2019/01/FNAC.png)

# Installation

## WordPress sous Docker avec Traefik

Traefik se charge de générer le certificat SSL avec Let's Encrypt pour votre site WordPress, ce qui veux dire que le certificat est déjà présent sur le site. Il ne reste plus qu'à faire les redirections HTTP vers HTTPS et faire quelques petites modifications.

Pour cela, nous allons utiliser une extension qui va s'occuper de tout ça à notre place : **Really Simple SSL**

![](/images/wordpress/ssl/wordpress_ssl_1.png)

Pour installer l'extension, allez dans : `Extensions` > `Ajouter` > `Chercher` > `Really Simple SSL` > `Installer Maintenant` > `Activer`

Une fois l'extension activée, vous devriez apercevoir un bandeau avec ce message : 

![](/images/wordpress/ssl/wordpress_ssl_2.png)

Cliquez simplement sur “`Allez-y, activez SSL !`” , ouvrez votre site dans un nouvel onglet et admirer ce si jolie petit cadenas 🔒 !

> Une fois le SSL activé sur votre site, vous pouvez désinstaller l'extension si vous le souhaitez.
{.is-info}


## WordPress sans Traefik

Tu sais faire ? N'hésite pas à nous partager la procédure 😃

## WordPress chez Infomaniak

Infomaniak peux vous fournir directement des certificats SSL, pour cela rendez vous dans votre manager Infomaniak et dans la gestion de vos Hébergements Web. Puis cliquez sur “**Changer de certificat**” : 

![](/images/wordpress/ssl/wordpress_ssl_2.0.png)

Vous pourrez choisir entre différents certificats SSL :

![](/images/wordpress/ssl/wordpress_ssl_2.1.png)

Une fois le certificat sur l'hébergement, il ne vous reste plus qu'à l'activer sur WordPress.

Pour cela, nous allons utiliser une extension qui va s'occuper de tout ça à notre place : **Really Simple SSL**

![](/images/wordpress/ssl/wordpress_ssl_1.png)

Pour installer l'extension, allez dans : `Extensions` > `Ajouter` > `Chercher` > `Really Simple SSL` > `Installer Maintenant` > `Activer`

Une fois l'extension activée, vous devriez apercevoir un bandeau avec ce message : 

![](/images/wordpress/ssl/wordpress_ssl_2.png)

Cliquez simplement sur “`Allez-y, activez SSL !`” , ouvrez votre site dans un nouvel onglet et admirer ce si jolie petit cadenas 🔒 !

> Une fois le SSL activé sur votre site, vous pouvez désinstaller l'extension si vous le souhaitez.
{.is-info}


## WordPress chez OVH

Alors déjà, le mieux à faire : passer chez [Infomaniak](https://www.infomaniak.com/fr) ! 😉

Bon … Vous êtes vraiment ici pour le faire chez OVH ? Allons y …

Rendez-vous sur le panel OVH afin de commander ou générer un certificat SSL :

![](/images/wordpress/ssl/wordpress_ssl_3.0.png)

Une fois le certificat sur l'hébergement, il ne vous reste plus qu'à l'activer sur WordPress.

Pour cela, nous allons utiliser une extension qui va s'occuper de tout ça à notre place : **Really Simple SSL**

![](/images/wordpress/ssl/wordpress_ssl_1.png)

Pour installer l'extension, allez dans : `Extensions` > `Ajouter` > `Chercher` > `Really Simple SSL` > `Installer Maintenant` > `Activer`

Une fois l'extension activée, vous devriez apercevoir un bandeau avec ce message : 

![](/images/wordpress/ssl/wordpress_ssl_2.png)

Cliquez simplement sur “`Allez-y, activez SSL !`” , ouvrez votre site dans un nouvel onglet et admirer ce si jolie petit cadenas 🔒 !

> Une fois le SSL activé sur votre site, vous pouvez désinstaller l'extension si vous le souhaitez.
{.is-info}
