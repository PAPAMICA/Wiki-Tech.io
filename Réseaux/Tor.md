---
title: Tor : opérer un middle relay
description: Opérez un noeud Tor sous Debian
published: false
date: 2021-05-11T19:44:29.982Z
tags: linux, tor, réseau
editor: markdown
dateCreated: 2021-05-11T19:26:41.152Z
---

# Avant propos

Ce tutorial n’a pas pour but d’enseigner ce qu’est Tor ou comment vous y rendre : la documentation officielle de Tor et des milliers d’autres ressources sont à votre disposition sur Internet et seront crtainement de meilleure qualité que ce que je pourrais écrire ici. Commencez donc par lire du contenu.

La page Wikipedia

Ce wiki a pour but de vous montrer à vous, utilisateur intermédiaire de l’outil, comment aller plus loin. Ce n’est pas un guide pour débutant et encore moins un tuto Linux. Des bases en Linux sont necessaires avant de s’attaquer à Tor sous Linux.

### I. Opérer un nœud Tor ###

Comme vous l’avez lu dans la documentaiton, lorsque vous vous connetez à Tor, une « route » est créée à l’intérieur du réseau. Les paquets sortant de votre poste sont chiffrés par trois fois (donc réencapsulées trois fois en AES localement) et à chauque nouveau « bond » (« hops » en aglais / dans le jargon) une couche de chiffrement est retirée du paquet par les différents intermédiaires. Une fois arrivé à destination, la dernière couche est retirée afin que le host à qui vous vouliez communiquer au final puisse lire l’information en clair. Cette route n’est pas définitive et en fonction de votre configuration dans /etc/tor/torrc elle sera plus oui moins éphémère. Une fois expirée, une nouvelle route sera créée par le client et ainsi de suite. Laissez les réglages par défaut à ce sujet.

Il existe plusieurs types de relais (https://community.torproject.org/relay/types-of-relays/) et les héberger est plus ou moins dangereux pour l’opérateur (donc vous). Je vous déconseille plus que vivement d’héberger un nœud de sortie à moins que vous ne souhaitiez très fort que la police sonne à votre porte dans les heures à venir.  De multiples opérateurs dans des pays pourtant libres et civilisés on eu de très gros problèmes légaux. Vous êtes prévenus.

Un relais intermédiaire (middle relay) se charge de recevoir un paquet d’un côté à l’intérieur de Tor. Ce paquet est chiffré. Il retire une couche de chiffrement et le renvoie au prochain maillon de la chaine de l’autre côté. Le paquet n’est pas totalement déchiffré ce qui veut dire qu’entant qu’oprateur d’un nœud à ce niveau vous ne pouvez pas lire l’information qu’il contient. Ce type de nœud est plutôt simple à maintenir et pas très dangereux puisque votre relai est caché à l’intérieur de Tor, rien ne sort sur Internet. La localisation géographique de votre relai n’est jamais divulgée et votre identité non plus.

Avec le temps, votre relai va se voir confier de plus en plus de trafic à mesure que sa stabilité et sa fiabilité seront jugées « aptes » par l’algorythme du réseau. Si votre relai est robuste il se verra certainement attribuer le rôle « d’entry guard » (ou simplement « guard ») ce qui est une consécration puisque le rôle des guards est primordial pour la sécurité des utilisateurs de Tor et le fonctionnement du réseau.

### II. Création de la machine virtuelle ###

C’est parti ! 
 
Comme nous allons souvent le faire lorsqu’il s’agit de Tor, nous allons créer une machine virtuelle sur notre serveur. Je vous déconseille de faire fonctionner quoi que ce soit concernant Tor sur votre serveur en bare metal. Je préfère utiliser OpenBSD pour opérer des relais (principalement pour la possibilité de compiler Tor avec LibreSSL et pour le Secure Level 3) mais ce wiki est au sujet de Linux et surtout de Debian et c’est un système plus que passable pour cet usage bien qu’un peu de hardening soit necessaire à l’arrivée. Je n’ai pas utilisé Debian depuis 10 ans alors si vous voyez un truc choquant n’hésitez pas à le signaler.

Pour créer la VM, quelques conseil sur les ressources :

**HDD :** 5Go
**CPU :** Entre 2 et 4 threads devraient suffir sur un CPU moderne équipé des instructions AES-NI, tout dépend de l’amour que vous voulez donner à votre relai.
**Mémoire : **512Mo si vous opérez moins de 40Mb en bnande passante, 1024Mo pour 40Mb et plus. Nous prendrons le soin de désactiver ls services inutiles… Debian n’est pas trop bloat on devrait s’en sortir pas mal.
**Réseau :** Une interface réseau virtuelle avec un driver moderne. Un faites un bridge avec une interface physique pour exposer votre VM à votre LAN. Bien qu’aucune redirection ne sera necessaire il est toujours confortable de pouvoir accéder à votre machine via OpenSSH.

Uin relais Tor est extrêmement simple… N’installez que le strict necessaire sur votre machine :

` # apt install -y tor nyx net-tools sudo vim`

une adresse IP fixe n’est pas requise par Tor pour foncitonner correctement mais j’ai envie de pouvoir me connecter à ma VM en ssh :

* * * 

<pre><code>$ cat /etc/network/interfaces
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
    	address 192.168.1.17 
    	netmask 255.255.255.0
    	gateway 192.168.1.1
</code></pre>

On redémarre le réseau : 

`# systemctl restart networking
`

Le serveur doit impérativement être à l’heure. C’est un prérequis indispensable. Vérifiez bien que l’heure sur NTP est bien activée. Si ce n’est pas le cas il faut que vous l’activiez… Je trouve la gestion du NTP très pourrie sous Debian, je vous laisse faire vous-même. Choisissez un démon entre chrony, ntp et je ne sais quoi d'autre. Le résultat doit ressembler à ça : 







