---
title: MOTD
description: 
published: true
date: 2021-06-14T07:43:45.184Z
tags: 
editor: markdown
dateCreated: 2021-05-24T10:35:28.155Z
---

# Changer le MOTD

## Message statique

Sur Linux, à chaque connexion SSH, vous retrouvez un petit message d’accueil vous donnant quelques informations utiles.

Dans notre cas, nous voulons le changer afin de pouvoir identifier rapidement sur quels serveurs nous nous trouvons mais aussi afficher d’autres informations utiles.

Le texte qui s’affiche à chaque connexion SSH se trouve dans le fichier motd que vous pouvez retrouver ici : `/etc/motd`

```plaintext
nano /etc/motd
```

### Voici quelques exemple de MOTD statiques :

```plaintext
  ██╗    ██╗███████╗██╗      ██████╗ ██████╗ ███╗   ███╗███████╗
  ██║    ██║██╔════╝██║     ██╔════╝██╔═══██╗████╗ ████║██╔════╝
  ██║ █╗ ██║█████╗  ██║     ██║     ██║   ██║██╔████╔██║█████╗
  ██║███╗██║██╔══╝  ██║     ██║     ██║   ██║██║╚██╔╝██║██╔══╝
   ╚███╔███╔╝███████╗███████╗╚██████╗╚██████╔╝██║ ╚═╝ ██║███████╗
    ╚══╝╚══╝ ╚══════╝╚══════╝ ╚═════╝ ╚═════╝ ╚═╝     ╚═╝╚══════╝

               Server   : NURION

               IP       : XXX.XXX.XXX.XXX

               Provider : Infomaniak
```

```plaintext
  █████╗ ███╗   ██╗███████╗██╗██████╗ ██╗     ███████╗
██╔══██╗████╗  ██║██╔════╝██║██╔══██╗██║     ██╔════╝
███████║██╔██╗ ██║███████╗██║██████╔╝██║     █████╗  
██╔══██║██║╚██╗██║╚════██║██║██╔══██╗██║     ██╔══╝  
██║  ██║██║ ╚████║███████║██║██████╔╝███████╗███████╗
╚═╝  ╚═╝╚═╝  ╚═══╝╚══════╝╚═╝╚═════╝ ╚══════╝╚══════╝
 |-------------------------------------------------|
 |                    Bienvenue                    |
 |-------------------------------------------------|
 |-------------------    VPS    -------------------|
 |-------------------------------------------------|
 |                                                 |
 |          Server      Ansible                    |
 |                                                 |
 |          IP          XXX.XXX.XXX.XXX            |
 |                                                 |
 |          Provider    FirstHeberg                |
 |          Machine     vps-48235                  |
 |          Purpose     Prod Ansible               |
 |                                                 |
 |-------------------------------------------------|
```

## Message dynamique

Sinon il existe un petit soft qui permet d'avoir un MOTD dynamique :

```plaintext
apt-get install figlet
```