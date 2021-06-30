---
title: 🔐 WireGuard
description: Comprendre et installer ce VPN nouvelle génération
published: false
date: 2021-06-30T21:19:47.549Z
tags: linux, réseau, routage, vpn, wireguard
editor: markdown
dateCreated: 2021-06-27T21:09:12.144Z
---

![Logo WireGuard](https://www.wireguard.com/img/wireguard.svg)

# Présentation

WireGuard est un protocole VPN de nouvelle génération sous licence GPLv2 (ou MIT, BSD, Apache 2.0 ou GPL suivant le contexte) créé par Jason A. Donenfeld. Le site officiel étant accessible via ce lien : https://www.wireguard.com/.

WireGuard se veut être plus **simple**, **rapide** et **sécurisé** que les protocoles VPN communs que sont OpenVPN et IPsec. 
- La **simplicité** de configuration qui se passe en une seule phase standardisée contrairement à IPsec fonctionnant avec deux phases souvent difficiles à appréhender pour des novices. De plus la non utilisation de certificats, comme on la retrouve avec OpenVPN, simplifie la gestion à plus long terme et ne nécessite pas la création d'une PKI.
- La **sécurité** est assurée avec l'utilisation de primitives cryptographiques modernes mais aussi par la mise à disposition d'une seule combinaison de méthode de chiffrement. De plus le code complet de WireGuard tient sur environ 4 000 lignes ce qui facilite les audits de sécurité et réduit sa surface d'attaque.
- La **rapidité** tient en grande partie des points précédents mais aussi parce que WireGuard fonctionne au niveau du noyau (seulement sous Linux). Les tests présents sur le site officiel (https://www.wireguard.com/performance/) démontrent un très fort avantage de WireGuard sur OpenVPN et un avantage un peu plus ténu sur IPsec, à la fois en débit maximal atteint et en temps de latence. A savoir que d'autres tests sont disponibles sur Internet et tendent à placer WireGuard comme le plus performant même si l'écart n'est pas toujours aussi important.

> C'est un **VPN de niveau 3** du modèle OSI. C'est-à-dire que c'est la couche *Réseau* (IP, ICMP, ARP et DHCP principalement) qui est redirigée dans le tunnel VPN. Le client VPN pourra atteindre le réseau distant mais ne sera pas vu comme appartenant directement à ce réseau comme on l'aurait avec un VPN de niveau 2 (par exemple avec OpenVPN et l'utilisation de sa carte TAP).
{.is-info}


# Comment fonctionne WireGuard ?

## Quels sont les prérequis ?

WireGuard est intégré au noyau Linux à partir de la **version 5.6 du noyau Linux**. Toutes distributions ayant une version égale ou supérieur est donc compatible pour l'utilisation de WireGuard. Si ce n'est pas le cas, à partir du noyau en version 3.10, il est possible de compiler WireGuard avec les informations disponibles ici : https://www.wireguard.com/compilation/.
Une recherche sur Internet pour vérifier que sa distribution favorite est compatible avec WireGuard est tout de même conseillée. 

Pour les autres environnements il est possible d'aller vérifier sur le site WireGuard qu'un portage existe : https://www.wireguard.com/install/. A titre informatif les autres OS principaux sont compatibles à partir de :
- Windows 7 / Windows Server 2012 
- macOS 10.14 Mojave
- Android 5.0
- iOS 12.0 / iPadOS 12.0

> Ces informations de compatibilités sont celles annoncées pour la date du 28/06/2021.
{.is-warning}


## Quels flux sont utilisés ?

WireGuard fonctionne uniquement avec le **protocole UDP** (ce qui est recommandé pour un VPN afin d'éviter les problèmes que provoque l'encapsulation TCP dans du TCP). 

Aucun port standard n'a été affecté à WireGuard par l'IANA ([liste des ports assignés](https://www.iana.org/assignments/service-names-port-numbers/service-names-port-numbers.txt)) mais les documentations utilisent généralement le port 51820.


## Qu'est-ce que le routage cryptographique ?

Le nouveau concept central autour du protocole WireGuard est celui du routage cryptographique. Le routage cryptographique associe des adresses IP ou des sous-réseaux à des clefs publiques.
Les clefs cryptographiques fonctionnent à peu près comme celles que l'on retrouve avec le protocole SSH : une paire de clef, l'une dite privé et l'autre publique, sont utilisées afin d'utiliser des mécanismes de chiffrement asymétrique.

Admettons la configuration suivante :

|         |  IP source  |  IP WireGuard | Clef privée | Clef publique |
|--------:|:-----------:|:-------------:|:-----------:|:-------------:|
| Serveur |  1.2.3.4/32 | 10.0.0.254/24 |     ABC     |      XYZ      |
|  Client |  6.7.8.9/32 |  10.0.0.1/24  |     123     |      789      |

Du côté du serveur une association entre l'IP WireGuard du client, *10.0.0.1/24*, sera faite avec sa clef publique, *789*. Le traitement des paquets du côté du serveur seront traités comme ceci :
- Tous paquets entrants seront déchiffrés, à l'aide de la clef privée du serveur *ABC*, et si l'utilisation de la clef publique *789* ainsi que l'IP WireGuard correspond à *10.0.0.1/24* alors les paquets seront acceptés et seront routés normalement.
- Tous paquets sortants ayant pour destination *10.0.0.1/24* seront chiffrées à l'aide de la clef publique associée à l'IP, ici *789*, et envoyée vers la destination via son IP source, ici *6.7.8.9/32*.

> A noter que l'exemple est très simpliste, en condition réelle une clef privée peut être associée à différentes IP ou réseaux pour l'interface WireGuard. De même plusieurs clients sont en général rattachés à un serveur, la non duplication des routes ou adresses est essentielle pour ne pas causer des problèmes de routage ainsi que des difficultés pour retrouver la clef publique du client. 
{.is-info}


# Installation et configuration 

> Cette section installation et configuration va globalement se focaliser sur Linux car WireGuard a été conçu pour ce dernier. Les compatibilités avec les autres OS étant des portages du code de base il vous faudra chercher les spécificités liées à votre OS si les indications suivantes ne sont pas suffisantes.
> Les projets créés pour piloter un WireGuard avec des outils et interfaces plus conviviales et abstractives seront aussi omises de cet article.
{.is-warning}


## Serveur

### Installer WireGuard

Comme indiqué dans la section [*Quels sont les prérequis ?*](https://wiki-tech.io/fr/S%C3%A9curit%C3%A9/WireGuard#quels-sont-les-pr%C3%A9requis) il vous faudra un OS Linux avec un noyau de version supérieure ou égale à 5.6.

Les installations les plus communes sont listées sur le site de WireGuard : https://www.wireguard.com/install/.
Par exemple pour un Ubuntu l'installation se réalisera avec la simple commande suivante :
``` bash
sudo apt install wireguard
```


### Créer une paire de clefs

Les mécanismes d'authentification de WireGuard se basant sur l'usage de clefs publique et privé il vous faut les générer avant de passer à la configuration du serveur. Les outils WireGuard proposent de se charger de leur génération, la clef privée peut être générée avec la commande suivante :

``` bash
wg genkey
```

Il est préférable d'enregistrer le résultat directement dans un fichier avec une redirection de ce type :
``` bash
wg genkey > privatekey
```

> La clef privée ne devant pas être accessible par d'autres utilisateurs il est fortement recommandé d'appliquer un droit de lecture/écriture seulement pour l'utilisateur propriétaire (`chmod 0600 privatekey`).  
{.is-warning}

La clef publique est générée à l'aide de la clef privée avec la commande suivante (attention à la double redirection à modifier si le nom des fichiers est différent de votre côté) :
``` bash
wg pubkey < privatekey > publickey
```

Nous pouvons raccourcir ces générations de clefs via la seule commande suivante : 
``` bash
wg genkey | tee privatekey | wg pubkey > publickey && chmod 0600 privatekey
```


### Configuration simplifiée avec wg-quick

`wg-quick` est un utilitaire fourni avec WireGuard qui permet de rapidement configurer une interface WireGuard en se basant sur le contenu d'un fichier de configuration. Les fichiers de configurations sont d'abord recherchés dans **/etc/wireguard/** et sont nommés **\<INTERFACE>.conf**. Habituellement nous retrouvons comme nom *wg0* avec son fichier de configuration */etc/wireguard/wg0.conf*, la suite des commandes se basera sur cette configuration.

> L'utilisation de la commande `wg-quick` ne nécessite pas forcément de placer les fichiers de configurations dans /etc/wireguard/.
{.is-info}

> Le nom des interfaces sous Linux ne doivent pas dépasser 15 caractères. Le nom de l'interface sera valide avec la forme suivante **\[a-zA-Z0-9_=+.-]{1,15}**.
{.is-warning}


Voici un exemple de fichier de configuration annoté pour expliquer les différents champs :
``` bash
# Configuration de l'interface WireGuard du serveur pour une utilisation avec wg-quick.
[Interface]
# Définition de l'adresse IP (IPv4 ou IPv6) d'écoute pour l'interface WireGuard.
# L'adresse doit être de la forme IP/MASQUE (exemple : 172.18.10.254/24).
# Il est possible d'ajouter plus d'une adresse sur l'interface WireGuard.
Address = ADDRESS1
#Address = ADDRESS2

# (Optionnel) Ajouter les serveurs DNS affectés à l'interface WireGuard.
# Plusieurs DNS peuvent être définis en les séparant par des virgules.
#DNS = DNS1, DNS2

# (Optionnel) Lors de l'arrêt de l'interface WireGuard sa configuration sera enregistrée en écrasant le fichier de configuration.
#SaveConfig = true

# (Optionnel) Commandes à exécuter avant/après le démarrage ou l'arrêt de l'interface WireGuard.
# %i sera remplacé par le nom de l'interface WireGuard.
# Ces options sont en générales utilisées pour définir des options particulières de DNS, pare-feu ou NAT.
#PreUp =
#PostUp = 
#PreDown =
#PostDown =

# (Optionnel) Si l'ajout des routes de l'interface WireGuard doit être réalisée dans une table particulière il est possible de la définir ici.
# Le paramètre "off" désactive la création des routes, par défaut c'est "auto" qui est utilisé.
#Table = auto

# (Optionnel) Ajoute un marquage des paquets sortant.
# Si défini à 0 ou "off" ce mécanisme sera désactivé.
# Il est possible de définir la valeur en hexadécimale en la préposant de "0x".
#FwMark = off

# (Optionnel) Port d'écoute du serveur WireGuard.
# Si non défini ce sera un port aléatoire disponible qui sera utilisé.
#ListenPort = 51820

# (Optionnel) Définition manuelle du MTU de l'interface WireGuard.
# Avec la surencapsultation de WireGuard le MTU doit être au maximum égale au MTU normal de l'interface locale moins 60 octets pour de l'IPv4 et moins 80 pour de l'IPv6.
#MTU = 1420

# Clef privée de l'interface WireGuard.
# Exemple : yAnz5TF+lXXJte14tji3zlMNq+hd2rYUIgJBgB3fBmk=
PrivateKey = PRIVATE_KEY


# Configuration des paires pouvant se connecter au serveur.
# Création d'autant de [Peer] qu'il y a de paires identifiés avec une clef publique à connecter.
[Peer]
# Clef publique du paire.
# Exemple : xTIBA5rboUvnH4htodjb6e697QjLERt1NAB4mZqp8Dg=
PublicKey = PUBLIC_KEY_PEER_1

# (Optionnel) Clef partagée entre le serveur et le paire, cette clef doit être issue de wg genpsk.
# Cette option a pour but d'ajouter une couche supplémentaire de chiffrement symétrique pour une résistance théorique post-quantique.  
#PresharedKey = PRESHAREDKEY_PEER_1

# Liste des IPs ou réseaux associés au paire séparés par des virgules.
# Doit être de la forme IP/MASQUE (172.18.10.254/24) ou RESEAU/MASQUE (172.18.10.0/24).
# L'utilisation du réseau 0.0.0.0/0 ou ::/0 indique que tout le trafic doit être redirigé vers le paire. Dans ce cas un seul paire peut être défini.
AllowedIPs = ALLOWED_IP_1_PEER_1, ALLOWED_IP_2_PEER_1

# (Optionnel) Permet d'envoyer des paquets vides à l'hôte au bout de x secondes (entre 1 et 65535).
# Cette option est à activer si les pare-feu ou les NAT ne maintiennent pas la session UDP pour cause d'inactivité trop longue.
#PersistentKeepalive = off

# (Optionnel) Défini l'adresse IP du paire.
# Ce paramètre sera automatiquement mis à jour par WireGuard dans tous les cas.
# Le paramètre est de la forme IP:PORT ou NOM:PORT.
#Endpoint = ENDPOINT_PEER_1


#[Peer]
#PublicKey = PUBLIC_KEY_PEER_2
#PresharedKey = PRESHAREDKEY_PEER_2
#AllowedIPs = ALLOWED_IP_1_PEER_2, ALLOWED_IP_2_PEER_2
#PersistentKeepalive = off
#Endpoint = ENDPOINT_PEER_2
```

Une fois la configuration prête il ne reste plus qu'à démarrer WireGuard avec la commande suivante s'il faut préciser un fichier de configuration :
``` 
wg-quick up /etc/wireguard/wg0.conf
```

Il est aussi possible de préciser simplement le nom de l'interface à démarrer (sous-entend qu'un fichier *.conf* soit présent dans */etc/wireguard/*) :
``` 
wg-quick up wg0
```

Pour arrêter l'interface WireGuard il suffit de remplacer `wg-quick up` par `wg-quick down` :
``` 
wg-quick down wg0
```


#### Utiliser wg-quick avec systemd

`wg-quick` déploie aussi le nécessaire afin de l'utiliser via systemd et la commande `systemctl`. Il est donc possible de démarrer, arrêter ainsi que de démarrer automatiquement un tunnel WireGuard.

> Pour fonctionner il faut impérativement renseigner les fichiers de configurations dans */etc/wireguard/*.
{.is-warning}

Pour démarrer un tunnel il faudra utiliser la commande suivante (en remplaçant le nom de l'interface par la votre) :
```
systemctl start wg-quick@wg0
```

Vous pouvez vérifier l'état de fonctionnement du service avec :
``` 
systemctl status wg-quick@wg0
```

Pour que le service démarre au démarrage du système ce sera la commande suivante à renseigner :
``` 
systemctl enable wg-quick@wg0
```

Pour arrêter le service ce sera la commande suivante :
``` 
systemctl stop wg-quick@wg0
```

Pour supprimer le démarrage automatique ce sera :
``` 
systemctl disable wg-quick@wg0
```


### Configuration manuelle

La configuration manuelle d'une interface WireGuard est parfaitement possible et les étapes suivantes seront à réaliser (en plus de l'[installation](https://wiki-tech.io/fr/S%C3%A9curit%C3%A9/WireGuard#installer-wireguard) et [génération des clefs](https://wiki-tech.io/fr/S%C3%A9curit%C3%A9/WireGuard#cr%C3%A9er-une-paire-de-clefs)) :
1. Création d'un fichier de configuration WireGuard
1. Création d'une nouvelle interface 
1. Assignation d'une adresse IP à l'interface
1. Application de la configuration sur la nouvelle interface
1. Activation de l'interface

Suite à ces actions d'autres configurations pourraient être mise en place :
- Activation du routage des paquets
- Configuration d'un NAT 
- Gestion fine de la table de routage

Le fichier de configuration WireGuard ressemble fortement à celui de `wg-quick` avec seulement certaines options qui ne sont pas disponibles :
``` bash
# Configuration de l'interface WireGuard du serveur.
[Interface]
# (Optionnel) Ajoute un marquage des paquets sortant.
# Si défini à 0 ou "off" ce mécanisme sera désactivé.
# Il est possible de définir la valeur en hexadécimale en la préposant de "0x".
#FwMark = off

# (Optionnel) Port d'écoute du serveur WireGuard.
# Si non défini ce sera un port aléatoire disponible qui sera utilisé.
#ListenPort = 51820

# Clef privée de l'interface WireGuard.
# Exemple : yAnz5TF+lXXJte14tji3zlMNq+hd2rYUIgJBgB3fBmk=
PrivateKey = PRIVATE_KEY


# Configuration des paires pouvant se connecter au serveur.
# Création d'autant de [Peer] qu'il y a de paires identifiés avec une clef publique à connecter.
[Peer]
# Clef publique du paire.
# Exemple : xTIBA5rboUvnH4htodjb6e697QjLERt1NAB4mZqp8Dg=
PublicKey = PUBLIC_KEY_PEER_1

# (Optionnel) Clef partagée entre le serveur et le paire, cette clef doit être issue de wg genpsk.
# Cette option a pour but d'ajouter une couche supplémentaire de chiffrement symétrique pour une résistance théorique post-quantique.  
#PresharedKey = PRESHAREDKEY_PEER_1

# Liste des IPs ou réseaux associés au paire séparés par des virgules.
# Doit être de la forme IP/MASQUE (172.18.10.254/24) ou RESEAU/MASQUE (172.18.10.0/24).
# L'utilisation du réseau 0.0.0.0/0 ou ::/0 indique que tout le trafic doit être redirigé vers le paire. Dans ce cas un seul paire peut être défini.
AllowedIPs = ALLOWED_IP_1_PEER_1, ALLOWED_IP_2_PEER_1

# (Optionnel) Permet d'envoyer des paquets vides à l'hôte au bout de x secondes (entre 1 et 65535).
# Cette option est à activer si les pare-feu ou les NAT ne maintiennent pas la session UDP pour cause d'inactivité trop longue.
#PersistentKeepalive = off

# (Optionnel) Défini l'adresse IP du paire.
# Ce paramètre sera automatiquement mis à jour par WireGuard dans tous les cas.
# Le paramètre est de la forme IP:PORT ou NOM:PORT.
#Endpoint = ENDPOINT_PEER_1


#[Peer]
#PublicKey = PUBLIC_KEY_PEER_2
#PresharedKey = PRESHAREDKEY_PEER_2
#AllowedIPs = ALLOWED_IP_1_PEER_2, ALLOWED_IP_2_PEER_2
#PersistentKeepalive = off
#Endpoint = ENDPOINT_PEER_2
```

Une fois le fichier de configuration créé et en place les commandes suivantes peuvent être exécutées pour créer et activer le tunnel WireGuard. A noter que des modifications seront peut-être nécessaire car le nom de l'interface créée sera *wg0* avec un fichier de configuration */etc/wireguard/wg0.conf* :
``` 
ip link add dev wg0 type wireguard
ip address add dev wg0 172.18.10.254/24
wg setconf wg0 /etc/wireguard/wg0.conf
ip link set up dev wg0
```

> Si vous définissez manuellement le MTU de l'interface WireGuard tâchez de soustraire 60 octets de votre MTU normal pour de l'IPv4 ou de soustraire 80 octets pour de l'IPv6.
> L'utilisation d'un tunnel provoque une surencapsulation qui réduit le MTU, si ce dernier était égale ou supérieur au MTU normal alors un paquet utile utilisera deux paquets pour le tunnel. 
{.is-warning}


Pour rappel si l'activation du routage des paquets entrant est souhaité (fonctionner comme un routeur) il faudra lancer les commandes suivantes :
```
sysctl -w net.ipv4.ip_forward=1
sysctl -w net.ipv6.ip_forward=1
```

Pour rendre cette configuration permanente vous pouvez placer les paramètres suivants dans */etc/sysctl.conf* et recharger la configuration avec `sysctl -p /etc/sysctl.conf` :
```
net.ipv4.ip_forward=1
net.ipv6.ip_forward=1
```


### Informations sur les clients connectés

WireGuard, avec la commande `wg`, permet de récupérer plusieurs informations sur l'état actuel des interfaces. Si la commande `wg` est lancée seule alors c'est un équivalent de `wg show all` qui détaillera la configuration de toutes les interfaces WireGuard. 

L'affichage des informations de configuration d'une interface particulière a pour commande : 
```
wg showconf wg0
```

L'affichage des informations d'un interface particulière rajoutera les informations d'heure de dernière connexion du paire, mais aussi sa consommation de données :
```
wg show wg0
```

> Pour toutes informations complémentaires sur les options non citées le manuel est disponible ici : [man 8 wg](https://git.zx2c4.com/wireguard-tools/about/src/man/wg.8).
{.is-info}


## Client

### Configuration du client sous Linux

#### Installer WireGuard

> WireGuard ne différenciant pas un client d'un serveur cette section est identique à celle de la configuration du serveur.
{.is-info}

L'installation du client est identique à celle du serveur, par conséquent les prérequis de la section [*Quels sont les prérequis ?*](https://wiki-tech.io/fr/S%C3%A9curit%C3%A9/WireGuard#quels-sont-les-pr%C3%A9requis) s'appliquent aussi. Il vous faudra un OS Linux avec un noyau de version supérieure ou égale à 5.6.

Les installations les plus communes sont listées sur le site de WireGuard : https://www.wireguard.com/install/.
Par exemple pour un Ubuntu l'installation se réalisera avec la simple commande suivante :
``` bash
sudo apt install wireguard
```


#### Créer une paire de clefs

> WireGuard ne différenciant pas un client d'un serveur cette section est identique à celle de la configuration du serveur.
{.is-info}

Les mécanismes d'authentification de WireGuard se basant sur l'usage de clefs publique et privé il vous faut les générer avant de passer à la configuration du serveur. Les outils WireGuard proposent de se charger de leur génération, la clef privée peut être générée avec la commande suivante :

``` bash
wg genkey
```

Il est préférable d'enregistrer le résultat directement dans un fichier avec une redirection de ce type :
``` bash
wg genkey > privatekey
```

> La clef privée ne devant pas être accessible par d'autres utilisateurs il est fortement recommandé d'appliquer un droit de lecture/écriture seulement pour l'utilisateur propriétaire (`chmod 0600 privatekey`).  
{.is-warning}

La clef publique est générée à l'aide de la clef privée avec la commande suivante (attention à la double redirection à modifier si le nom des fichiers est différent de votre côté) :
``` bash
wg pubkey < privatekey > publickey
```

Nous pouvons raccourcir ces générations de clefs via la seule commande suivante : 
``` bash
wg genkey | tee privatekey | wg pubkey > publickey && chmod 0600 privatekey
```


#### Utilisation de wg-quick

> WireGuard ne différenciant pas un client d'un serveur cette section est identique à celle de la configuration du serveur, le fichier d'exemple a tout de même été modifié dans ces annotations pour avoir une configuration plus spécifique pour les clients (le paramètre endpoint des paires est obligatoire par exemple).
{.is-info}

`wg-quick` est un utilitaire fourni avec WireGuard qui permet de rapidement configurer une interface WireGuard en se basant sur le contenu d'un fichier de configuration. Les fichiers de configurations sont d'abord recherchés dans **/etc/wireguard/** et sont nommés **\<INTERFACE>.conf**. Habituellement nous retrouvons comme nom *wg0* avec son fichier de configuration */etc/wireguard/wg0.conf*, la suite des commandes se basera sur cette configuration.

> L'utilisation de la commande `wg-quick` ne nécessite pas forcément de placer les fichiers de configurations dans /etc/wireguard/.
{.is-info}

> Le nom des interfaces sous Linux ne doivent pas dépasser 15 caractères. Le nom de l'interface sera valide avec la forme suivante **\[a-zA-Z0-9_=+.-]{1,15}**.
{.is-warning}


Voici un exemple de fichier de configuration annoté pour expliquer les différents champs :
``` bash
# Configuration de l'interface WireGuard du serveur pour une utilisation avec wg-quick.
[Interface]
# Définition de l'adresse IP (IPv4 ou IPv6) d'écoute pour l'interface WireGuard.
# L'adresse doit être de la forme IP/MASQUE (exemple : 172.18.10.254/24).
# Il est possible d'ajouter plus d'une adresse sur l'interface WireGuard mais rarement utile pour une configuration cliente.
Address = ADDRESS1
#Address = ADDRESS2

# (Optionnel) Ajouter les serveurs DNS affectés à l'interface WireGuard.
# Plusieurs DNS peuvent être définis en les séparant par des virgules.
#DNS = DNS1, DNS2

# (Optionnel) Lors de l'arrêt de l'interface WireGuard sa configuration sera enregistrée en écrasant le fichier de configuration.
#SaveConfig = true

# (Optionnel) Commandes à exécuter avant/après le démarrage ou l'arrêt de l'interface WireGuard.
# %i sera remplacé par le nom de l'interface WireGuard.
# Ces options sont en générales utilisées pour définir des options particulières de DNS, pare-feu ou NAT.
#PreUp =
#PostUp = 
#PreDown =
#PostDown =

# (Optionnel) Si l'ajout des routes de l'interface WireGuard doit être réalisée dans une table particulière il est possible de la définir ici.
# Le paramètre "off" désactive la création des routes, par défaut c'est "auto" qui est utilisé.
#Table = auto

# (Optionnel) Ajoute un marquage des paquets sortant.
# Si défini à 0 ou "off" ce mécanisme sera désactivé.
# Il est possible de définir la valeur en hexadécimale en la préposant de "0x".
#FwMark = off

# (Optionnel) Port d'écoute du serveur WireGuard.
# Si non défini ce sera un port aléatoire disponible qui sera utilisé.
# Pour une configuration cliente l'utilisation d'un port aléatoire convient parfaitement.
#ListenPort = 51820

# (Optionnel) Définition manuelle du MTU de l'interface WireGuard.
# Avec la surencapsultation de WireGuard le MTU doit être au maximum égale au MTU normal de l'interface locale moins 60 octets pour de l'IPv4 et moins 80 pour de l'IPv6.
#MTU = 1420

# Clef privée de l'interface WireGuard.
# Exemple : yAnz5TF+lXXJte14tji3zlMNq+hd2rYUIgJBgB3fBmk=
PrivateKey = PRIVATE_KEY


# Configuration des paires auquel le client se connectera.
# Le paire correspond à un serveur WireGuard, en général un seul est défini.
# Création d'autant de [Peer] qu'il y a de paires identifiés avec une clef publique à connecter.
[Peer]
# Clef publique du paire.
# Exemple : xTIBA5rboUvnH4htodjb6e697QjLERt1NAB4mZqp8Dg=
PublicKey = PUBLIC_KEY_PEER_1

# (Optionnel) Clef partagée entre le serveur et le paire, cette clef doit être issue de wg genpsk.
# Cette option a pour but d'ajouter une couche supplémentaire de chiffrement symétrique pour une résistance théorique post-quantique.  
#PresharedKey = PRESHAREDKEY_PEER_1

# Liste des IPs ou réseaux associés au paire séparés par des virgules.
# Doit être de la forme IP/MASQUE (172.18.10.254/24) ou RESEAU/MASQUE (172.18.10.0/24).
# L'utilisation du réseau 0.0.0.0/0 ou ::/0 indique que tout le trafic doit être redirigé vers le paire. Dans ce cas un seul paire peut être défini.
# C'est cette option qui définiera les routes devant passées par le tunnel WireGuard
AllowedIPs = ALLOWED_IP_1_PEER_1, ALLOWED_IP_2_PEER_1

# (Optionnel) Permet d'envoyer des paquets vides à l'hôte au bout de x secondes (entre 1 et 65535).
# Cette option est à activer si les pare-feu ou les NAT ne maintiennent pas la session UDP pour cause d'inactivité trop longue.
#PersistentKeepalive = off

# Défini l'adresse IP du paire.
# Le paramètre est de la forme IP:PORT ou NOM:PORT et défini le serveur WireGuard vers lequel se connecter.
Endpoint = ENDPOINT_PEER_1


#[Peer]
#PublicKey = PUBLIC_KEY_PEER_2
#PresharedKey = PRESHAREDKEY_PEER_2
#AllowedIPs = ALLOWED_IP_1_PEER_2, ALLOWED_IP_2_PEER_2
#PersistentKeepalive = off
#Endpoint = ENDPOINT_PEER_2
```

Une fois la configuration prête il ne reste plus qu'à démarrer WireGuard avec la commande suivante s'il faut préciser un fichier de configuration :
``` 
wg-quick up /etc/wireguard/wg0.conf
```

Il est aussi possible de préciser simplement le nom de l'interface à démarrer (sous-entend qu'un fichier *.conf* soit présent dans */etc/wireguard/*) :
``` 
wg-quick up wg0
```

Pour arrêter l'interface WireGuard il suffit de remplacer `wg-quick up` par `wg-quick down` :
``` 
wg-quick down wg0
```


##### Utiliser wg-quick avec systemd

> WireGuard ne différenciant pas un client d'un serveur cette section est identique à celle de la configuration du serveur.
{.is-info}

`wg-quick` déploie aussi le nécessaire afin de l'utiliser via systemd et la commande `systemctl`. Il est donc possible de démarrer, arrêter ainsi que de démarrer automatiquement un tunnel WireGuard.

> Pour fonctionner il faut impérativement renseigner les fichiers de configurations dans */etc/wireguard/*.
{.is-warning}

Pour démarrer un tunnel il faudra utiliser la commande suivante (en remplaçant le nom de l'interface par la votre) :
```
systemctl start wg-quick@wg0
```

Vous pouvez vérifier l'état de fonctionnement du service avec :
``` 
systemctl status wg-quick@wg0
```

Pour que le service démarre au démarrage du système ce sera la commande suivante à renseigner :
``` 
systemctl enable wg-quick@wg0
```

Pour arrêter le service ce sera la commande suivante :
``` 
systemctl stop wg-quick@wg0
```

Pour supprimer le démarrage automatique ce sera :
``` 
systemctl disable wg-quick@wg0
```


#### Configuration manuelle

> WireGuard ne différenciant pas un client d'un serveur cette section est identique à celle de la configuration du serveur.
{.is-info}

##### Configuration avancée : utilisation des espaces de noms réseaux

### Configuration du client sur les autres plateformes

#### Générer le QR code de configuration



---
*Rédacteur Lucas MEYER*