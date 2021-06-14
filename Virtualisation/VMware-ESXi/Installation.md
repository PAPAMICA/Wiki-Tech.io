---
title: Installation
description: Installation de l'hyperviseur ESXi (6.0 / 6.5 / 6.7 / 7.0)
published: true
date: 2021-06-14T08:16:00.725Z
tags: 
editor: markdown
dateCreated: 2021-05-24T10:37:41.896Z
---

![](/images/logo-esxi.png)

# Installation de VMware ESXi 7.0u1

# Prérequis

-   Un serveur bare-metal ou une machine virtuelle avec nested virtualization
-   L'ISO de VMware ESXi : [https://my.vmware.com/en/web/vmware/evalcenter?p=free-esxi7](https://my.vmware.com/en/web/vmware/evalcenter?p=free-esxi7)
-   Matériel compatible avec ESXi : [https://www.vmware.com/resources/compatibility/search.php](https://www.vmware.com/resources/compatibility/search.php)
-   Clé USB bootable, un CD ou boot par le réseau (comme avec iLO).

# Installation

Une fois l'ISO démarrée, on presse Entrée.

![](/images/demarrage-esxi.png)

Une fois l'installateur chargé, on presse Entrée, on accepte les conditions puis on choisit notre disque en appuyant sur Entrée.

![](/images/debut_installation_esxi.png)

![](/images/choix-disque-esxi.png)

![](/images/choix-langue_clavier.png)

On définit ensuite le mot de passe root.

![](/images/choix-motdepasse-root.png)

On confirme l'installation.

![](/images/confirmation-installation.png)

L'installation est terminée, il reste plus qu'à redémarrer !

![](/images/fin-installation-esxi.png)

![](/images/premier-demarrage-esxi.png)

# Configuration préalable

Tout d'abord, on va attribuer une adresse IP fixe et un nom d'hôte à notre ESXI. On presse F2, on rentre le mot de passe root.

On choisit ensuite l'option “Configure Management Network”. On modifie l'IPv4, le serveur DNS si nécessaire et l'IPv6 si vous l'utilisez. Modifiez également le nom d'hôte dans “DNS Configuration”.

![](/images/adressage_ip_esxi.png)

En rentrant l'adresse IP dans un navigateur, on arrive sur l'interface web de l'ESXi.

![](/images/web-interface-esxi.png)

Rentrez-ici le compte root. 

![](/images/web-esxi.png)

[Source d'origine](https://docs.khroners.fr/books/vmware/page/esxi-7-installation) | *Rédacteur Alexis BONNET*