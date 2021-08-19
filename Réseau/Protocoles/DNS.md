---
title: Serveur DNS
description: Qu’est ce qu’un serveur DNS et à quoi sert-il ?
published: true
date: 2021-06-14T08:07:49.361Z
tags: 
editor: markdown
dateCreated: 2021-05-24T10:36:36.627Z
---

![Why Use Azure DNS? | Aidan Finn, IT Pro](https://aidanfinn.com/wp-content/uploads/2017/12/Azure-DNS-1024x1024.png =15%x)

# Le serveur DNS

Comme vous avez pu le lire dans l’article « C’est quoi une adresse IP ?« , les ordinateurs échangent entre eux grâce à leurs adresses IP. Et pour retrouver les adresses IP, ils demandent au DNS.

## Définition et rôle du DNS

Tout d’abord : DNS veut dire « Domain Name System » ou système de nom de domaine. Nous allons d’abord voir le fonctionnement, puis nous aborderons la notion de nom de domaine (ou de sous-domaine).

Pour vous expliquer, le rôle et le fonctionnement d’un serveur DNS, je vais reprendre l’exemple du téléphone.

-   Vous souhaitez appeler quelqu’un dont vous ne connaissez pas le n° de téléphone ?
-   Que faites-vous ? ….
-   …. vous utilisez un annuaire !

Et bien voila, un serveur DNS est un annuaire pour ordinateur. Lorsque vous voulez accéder à un ordinateur dans le réseau, votre ordinateur va interroger le serveur DNS pour récupérer l’adresse de l’ordinateur que vous voulez joindre. Une fois, que votre ordinateur aura récupéré l’adresse du destinataire, il pourra le joindre directement avec son adresse IP.

Le serveur DNS va permettre de faire la relation entre nom d’ordinateur et adresse IP. *(ramené au téléphone, on obtient : nom de l’abonné et n°).*

Vous allez me dire :

-    *“mais je ne cherche jamais à joindre un ordinateur dans le réseau ! “*

A quoi, je vais vous répondre :

-    *” bien sur que si ! “*
-    *” Actuellement vous lisez ces lignes qui ne sont stockés pas sur votre ordinateur, donc votre ordinateur s’est connecté à un serveur pour récupérer les pages qui sont affichées sur votre écran. “*

## Exemple

Allons-y par l’exemple et un petit schéma, pour voir comment ça se passe lorsque vous surfez sur Internet et à quel moment intervient le serveur DNS.

![dns](https://i0.wp.com/www.culture-informatique.net/WordPress3/wp-content/uploads/2012/10/Requ%C3%AAte-Dns.png?resize=574%2C501&ssl=1)

Dans l’exemple ci-dessus, on voit que la requête « quelle est l’adresse de www.google.fr » a répondu 74.125.230.248. Cette requête s’appelle une résolution de nom de domaine.

Si l’on poursuit, on peut constater que le serveur DNS n’est utilisé que sur la partie 1-Question et 2-Réponse. Une fois que l’ordinateur a récupéré l’adresse du serveur à joindre, il n’a plus besoin du serveur DNS.

Cette adresse IP n’est pas une adresse au hasard. Elle correspond bien à un des serveurs de Google. (il y en a évidemment un grand nombre).  
Donc, si dans votre dans la barre d’adresse de votre navigateur, vous tapez : [http://74.125.206.94](http://74.125.206.94/), la page de recherche de Google doit s’afficher.

## Et les DNS chez moi ?

La plupart des personnes ignorent cette notion est c’est bien compréhensible. Alors comment cela peut-il fonctionner chez moi puisque je n’ai jamais renseigné ces serveurs ?

Les serveurs DNS fonctionnent en cascade. Pour expliquer cela on pourrait dire que lorsque qu’un serveur DNS ne connait pas la réponse, il va demander à son « parent ».

Chez vous, le serveur DNS est votre box (sauf si vous avez renseigné autre chose), et si votre box ne sait pas répondre, elle va interroger son parent : le(s) serveur(s) DNS de votre fournisseur d’accès. Cela peut remonter comme ça jusqu’aux serveurs DNS racines. (on va dire pour simplifier que les serveurs DNS racines sont les serveurs de base du système : on ne peut pas remonter plus haut.)

La configuration DNS de votre ordinateur (ou tablette, et tout autre équipement connecté chez vous) est fournie par le serveur [DHCP](https://labo-tech.fr/base-de-connaissance/quest-ce-quun-serveur-dhcp-et-a-quoi-sert-il/).

## Un fichier

Il existe un fichier sur votre ordinateur qui peut remplacer les requêtes DNS.

Ce fichier est le fichier « host » (hôte en français). Il va contenir la correspondance entre les adresses demandées et les adresses IP. Sur chaque ligne de ce fichier on va retrouver au début l’adresse IP suivi d’un espace ou d’une tabulation et ensuite du ou des nom(s) correspondant à cette adresse.  
*Exemple :  74.125.206.94 www.google.fr*

## La sécurité

Parlons juste un peu de sécurité.

Pour bien comprendre de quoi je veux parler, je vais reprendre l’exemple du téléphone :

-   Imaginez que vous ayez entre les mains un faux annuaire (piraté) ou les numéros de téléphone ne soient pas les bons, et vous renvoie vers des pirates.
-   Vous ayez besoin d’appeler votre banque : Vous appelez le numéro de téléphone et vous allez forcément tomber sur des pirates (mais vous ne le savez pas).
-   Au bout du fil, on peut vous demander des informations confidentielles, et vous les donnerez en tout confiance !!!

Il faut bien comprendre que cela peut être pareil avec des serveurs DNS et qu’il existe des virus qui modifie vos paramètres réseaux ou votre fichier « hosts » pour faire pointer les requêtes DNS vers des serveurs pirates.

Vous verrez que les navigateurs peuvent afficher des messages lorsque cela se produit.

Cela est tout à fait possible aussi sur les réseau Wi-Fi non sécurisé !! L’utilisation d’un VPN dans ces cas là est fortement recommandé !

# Changer de DNS

Sachez qu’il est tout à fait possible de changer de DNS. Lorsque vous utilisez votre connexion Internet, les adresses des serveurs DNS sont généralement ceux de votre FAI, mais sachez qu’il est possible d’en changer. Vous trouverez ci-dessous des sites proposant des listes de serveurs :

-   [http://public-dns.info/nameserver/fr.html](http://public-dns.info/nameserver/fr.html)

et je peux également vous en donner quelques un très connus :

-   OpenDNS : 208.67.220.222  et 208.67.222.220
-   CloudFlare : 1.1.1.1
-   Quad9 : 9.9.9.9
-   Google : 8.8.8.8 et 8.8.4.4

## Pourquoi changer de DNS ?

Je vois 2 raisons principales pour changer de DNS :

-   Si vous avez peur d’être « espionné », (car un serveur DNS) sait exactement quels sites vous consultez, mais il peut également vous renvoyer vers les sites qu’il a choisi.
-   Si vous trouvez votre accès un peu lent, (car rappelez-vous un serveur DNS est appelé à chaque page consultée) et plus il mettra du temps à répondre, plus la page mettra du temps à s’afficher.