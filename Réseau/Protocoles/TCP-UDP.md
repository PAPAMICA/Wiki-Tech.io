---
title: TCP & UDP
description: A quoi ça sert et quels sont les principaux ports ?
published: true
date: 2021-06-14T08:02:40.392Z
tags: 
editor: markdown
dateCreated: 2021-05-24T10:36:47.591Z
---

![TCP Ports list 1.2 Télécharger APK Android | Aptoide](https://cdn6.aptoide.com/imgs/3/2/4/32405f5d90379cf942c53bd29b67b8be_icon.png)

# Les protocoles TCP & UDP

TCP et UDP sont les 2 principaux protocoles de la couche transport. La différence entre TCP et UDP sont fondamentales.

Ces deux protocoles servent à échanger des paquets d’information entre 2 machines en utilisant leur adresse **IP** et un numéro de **port**.

## Protocole TCP

TCP fonctionne un peu comme le téléphone : il faut d’abord établir une connexion TCP entre les 2 machines, ce qu’on pourrait comparer à composer le numéro de téléphone.

Une fois que la communication est établie, les 2 machines peuvent dialoguer de manière bidirectionnelle (vous pouvez parler à votre interlocuteur, et c’est réciproque).

Et vous pouvez communiquer de cette manière autant que vous voulez, tant que vous ne fermez pas la connexion TCP (i.e. tant que vous ne raccrochez pas le combiné téléphonique).

On dit que TCP est un protocole **stateful** (*avec état*) : l’état c’est tout simplement la connexion, le fait que les 2 machines sont reliées par un circuit logique à travers internet. En programmation, cette connexion est matérialisée par une socket qui représente l’interface de communication avec la machine distante.

### Utilisation de TCP

TCP sert de socle à de nombreux protocoles de la couche application, que vous connaissez peut-être :

-   HTTP, qui sert à accéder aux sites internet (autrement dit : le web)
-   FTP, qui sert à échanger des fichiers entre 2 ordinateurs
-   POP3 et IMAP qui sert à lire ses emails
-   SMTP qui sert quant à lui à envoyer des emails

## Protocole UDP

UDP est un protocole **stateless** (*sans état*), on peut le comparer au courrier : vous placez le message à envoyer dans une enveloppe qui contient toutes les informations nécessaires au routage : l’adresse IP et le port (i.e. les coordonnées du destinataire), puis vous envoyez l’enveloppe.

Le paquet UDP (l’enveloppe) va alors être acheminé à travers internet jusqu’à sa destination.

### Utilisation d’UDP

UDP est utilisé pour :

-   DNS, le protocole de résolution des noms de domaines qui permet de connaître l’adresse IP d’un serveur à partir de son nom de domaine (exemple: www.google.fr)
-   Wake on LAN, qui permet d’allumer un ordinateur à l’aide d’un paquet magique

# Les principaux protocoles TCP/UDP

Voici dans un tableau les principaux protocoles TCP/UDP et leur port. Cette liste est personnelle, si vous voulez que d’autres protocoles y soit référencé n’hésitez pas à me le demander 🙂

| **n° port** | **Protocole** | **Service associé** |
| --- | --- | --- |
| 20  | tcp | ftp-data - File Transfer Protocol |
| 21  | tcp | ftp - File Transfer Protocol (le flux de contrôle pour le transfert de fichiers) |
| 22  | tcp | SSH - Secure Shell |
| 23  | tcp | Telnet |
| 25  | tcp | SMTP - Simple Mail Transfer Protocol (sans authentification) |
| 53  | udp | DNS - Domain Name Service |
| 67  | udp | DHCP (Client) - Dynamic Host Configuration Protocol |
| 68  | udp | DHCP (Serveur) - Dynamic Host Configuration Protocol |
| 69  | udp | tftp - Trivial File Transfer |
| 80  | tcp | HTTP - Hypertext Transfer Protocol |
| 110 | tcp | pop3 - Post Office Protocol v3 |
| 123 | udp | ntp - Network Time Protocol |
| 137 | tcp | netbios-ns - NETBIOS Name Service |
| 143 | tcp | imap4 - Internet Message Access Protocol v4 |
| 161 | udp | SNMP - Simple Network Management Protocol |
| 162 | udp | SNMPtrap - Simple Network Management Protocol Trap |
| 389 | tcp | LDAP - Lightweight Directory Access Protocol |
| 443 | tcp | https - HyperText Transfer Protocol Secure |
| 445 | tcp | CIFS - Common Internet File System (SMB - Samba) |
| 546 | udp | DHCPv6 - Dynamic Host Configuration Protocol v6 |
| 993 | tcp | IMAPS |
| 995 | tcp | POP3S |
| 1433 | tcp | Microsoft SQL Server |
| 1521 | tcp | Oracle SQL |
| 3306 | tcp | MySQL |
| 5432 | tcp | PostgreSQL |
| 5900 | tcp | VNC Server |
| 6667 | tcp | IRC |