---
title: Tor : opérer un middle relay
description: Opérez un noeud Tor sous Debian
published: false
date: 2021-05-11T19:26:41.152Z
tags: linux, tor, réseau
editor: markdown
dateCreated: 2021-05-11T19:26:41.152Z
---

# Avant propos

Ce tutorial n’a pas pour but d’enseigner ce qu’est Tor ou comment vous y rendre : la documentation officielle de Tor et des milliers d’autres ressources sont à votre disposition sur Internet et seront crtainement de meilleure qualité que ce que je pourrais écrire ici. Commencez donc par lire du contenu.

La page Wikipedia

Ce wiki a pour but de vous montrer à vous, utilisateur intermédiaire de l’outil, comment aller plus loin. Ce n’est pas un guide pour débutant et encore moins un tuto Linux. Des bases en Linux sont necessaires avant de s’attaquer à Tor sous Linux.

**I. Opérer un nœud Tor**

Comme vous l’avez lu dans la documentaiton, lorsque vous vous connetez à Tor, une « route » est créée à l’intérieur du réseau. Les paquets sortant de votre poste sont chiffrés par trois fois (donc réencapsulées trois fois en AES localement) et à chauque nouveau « bond » (« hops » en aglais / dans le jargon) une couche de chiffrement est retirée du paquet par les différents intermédiaires. Une fois arrivé à destination, la dernière couche est retirée afin que le host à qui vous vouliez communiquer au final puisse lire l’information en clair. Cette route n’est pas définitive et en fonction de votre configuration dans /etc/tor/torrc elle sera plus oui moins éphémère. Une fois expirée, une nouvelle route sera créée par le client et ainsi de suite. Laissez les réglages par défaut à ce sujet.

Il existe plusieurs types de relais (https://community.torproject.org/relay/types-of-relays/) et les héberger est plus ou moins dangereux pour l’opérateur (donc vous). Je vous déconseille plus que vivement d’héberger un nœud de sortie à moins que vous ne souhaitiez très fort que la police sonne à votre porte dans les heures à venir.  De multiples opérateurs dans des pays pourtant libres et civilisés on eu de très gros problèmes légaux. Vous êtes prévenus.

Un relais intermédiaire (middle relay) se charge de recevoir un paquet d’un côté à l’intérieur de Tor. Ce paquet est chiffré. Il retire une couche de chiffrement et le renvoie au prochain maillon de la chaine de l’autre côté. Le paquet n’est pas totalement déchiffré ce qui veut dire qu’entant qu’oprateur d’un nœud à ce niveau vous ne pouvez pas lire l’information qu’il contient. Ce type de nœud est plutôt simple à maintenir et pas très dangereux puisque votre relai est caché à l’intérieur de Tor, rien ne sort sur Internet. La localisation géographique de votre relai n’est jamais divulgée et votre identité non plus.

Avec le temps, votre relai va se voir confier de plus en plus de trafic à mesure que sa stabilité et sa fiabilité seront jugées « aptes » par l’algorythme du réseau. Si votre relai est robuste il se verra certainement attribuer le rôle « d’entry guard » (ou simplement « guard ») ce qui est une consécration puisque le rôle des guards est primordial pour la sécurité des utilisateurs de Tor et le fonctionnement du réseau.


