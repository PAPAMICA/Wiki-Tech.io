---
title: Vérifier son débit
description: Vérifier son débit ou la vitesse de transfert entre deux serveurs
published: true
date: 2021-04-30T21:28:58.690Z
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

### Installation :
# Tabs {.tabset}
## Debian / Ubuntu
```
curl -s https://install.speedtest.net/app/cli/install.deb.sh | sudo bash
sudo apt-get install speedtest
```
## CentOS / Fedora / Redhat
```
curl -s https://install.speedtest.net/app/cli/install.rpm.sh | sudo bash
sudo yum install speedtest
```
## FreeBSD
```
sudo pkg update && sudo pkg install -g libidn2 ca_root_nss
sudo pkg add "https://install.speedtest.net/app/cli/ookla-speedtest-1.0.0-freebsd.pkg"
```
## MacOS
```
brew tap teamookla/speedtest
brew update
brew install speedtest --force
```