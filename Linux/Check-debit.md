---
title: Vérifier son débit
description: Vérifier son débit ou la vitesse de transfert entre deux serveurs
published: true
date: 2021-05-20T14:34:33.495Z
tags: 
editor: markdown
dateCreated: 2021-04-30T21:13:55.147Z
---

![Mesurez votre débit Internet en ligne de commande – Korben](https://i2.wp.com/korben.info/app/uploads/2019/05/Speedtest-horizontal-black.png?fit=800%2C268&ssl=1){.image}

# Tester son débit

## Depuis un navigateur internet

Le plus simple et le plus rapide reste le test depuis un site internet, il en existe plusieurs :

-   [Nperf.com](https://www.nperf.com/fr/) : mon préféré.
-   [Speedtest.net](https://www.speedtest.net/fr) : le plus connu
-   [Fast.com](https://fast.com/fr/) : pour tester son débit aux serveurs Netflix

## Depuis son invite de commande

Speedtest d'Ookla met à disposition un petit outil à disposition très pratique pour tester son débit directement dans son terminal ou dans un script : [speedtest-cli](https://www.speedtest.net/fr/apps/cli)

### Installation
### Tabs {.tabset}
#### Debian / Ubuntu
```
curl -s https://install.speedtest.net/app/cli/install.deb.sh | sudo bash
sudo apt-get install speedtest
```
#### CentOS / Fedora / Redhat
```
curl -s https://install.speedtest.net/app/cli/install.rpm.sh | sudo bash
sudo yum install speedtest
```
#### FreeBSD
```
sudo pkg update && sudo pkg install -g libidn2 ca_root_nss
sudo pkg add "https://install.speedtest.net/app/cli/ookla-speedtest-1.0.0-freebsd.pkg"
```
#### MacOS
```
brew tap teamookla/speedtest
brew update
brew install speedtest --force
```

### Utilisation
Tout simplement :
```
speedtest

Testing from Infomaniak Network SA (195.15.XXX.XXX)...
Retrieving speedtest.net server list...
Selecting best server based on ping...
Hosted by REFO Falck (Falck) [257.26 km]: 15.042 ms
Testing download speed................................................................................
Download: 814.35 Mbit/s
Testing upload speed................................................................................................
Upload: 286.91 Mbit/s
```

# Tester le débit entre deux machines
Dans de nombreux cas, les problèmes peuvent subvenir à cause d’un problème réseau et/ou d’un mauvais débit.

Afin de vérifier ce point, nous pouvons tester le débit entre deux machines linux avec ssh (qui est installé dans 90% des cas par défaut et est même disponible pour Windows).

Remplacez user par votre utilisateur et server par l’ip de votre machine distante :
```
dd if=/dev/zero of=test bs=500M count=1; scp test user@server:/dev/null;
```
Rentrez le mot de passe de l’utilisateur sur la machine distante :
```
papamica@93.113.206.54’s password:
```
Le test s’effectue et affiche le débit :

`test                                               100%    500MB   370.5MB/s   00:03`