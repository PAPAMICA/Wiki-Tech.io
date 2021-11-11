---
title: Installation du client
description: 
published: true
date: 2021-11-03T09:44:30.490Z
tags: 
editor: markdown
dateCreated: 2021-05-24T10:33:58.495Z
---

![openstack-logo-horizontal.png](/images/cloud/openstack/openstack-logo-horizontal.png =1000x)
# Présentation

Pour une utilisation occasionnelle, le dashboard (Horizon) à l'aide d'un navigateur Web est plus simple, comme [documenté ici](https://docs.openstack.org/horizon/latest/user/index.html). 

Le client OpenStack unifié (openstack) est l'interface de ligne de commande recommandée. La plupart des exemples donnés dans ce livre sont basés sur cette commande. Nous vous recommandons d'utiliser les clients OpenStack les plus récents. Il existe également des versions disponibles pour les machines autonomes Mac, Windows et Linux. Pour installer les outils OpenStack sur votre propre ordinateur client, vous pouvez suivre l'une de ces méthodes :

# Linux

## Red Hat / Cent OS

```bash
sudo yum install python3-openstackclient
```

## Debian / Ubuntu

```bash
sudo apt install python3-openstackclient
```

## Using pip

```bash
pip3 install python3-openstackclient
```
Ou pour les anciens systèmes :

```bash
pip install python-openstackclient
```

# Mac OS

**Python 3.X Installation Guide**

(Facultatif) Si vous n’avez pas encore installé Homebrew sur votre système (cela peut prendre quelques minutes) :

```bash
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

(Facultatif) En utilisant brew, nous allons installer Python 3 : 

```bash
brew install python
```

Maintenant que Python 3 est installé, nous pouvons enfin installer les outils de ligne de commande OpenStack :

```bash
sudo pip3 install python-openstackclient
```

# Windows
Pour Windows vous devez aussi utilisez `pip` directement depuis Powershell :
```powershell
pip install -U buildtools
pip install python-openstackclient
```

# Vérifier l'installation

```bash
openstack --version
```

La commande doit renvoyer : `openstack <version>`