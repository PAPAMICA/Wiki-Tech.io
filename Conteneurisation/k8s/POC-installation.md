---
title: Installation d'un POC K8S sous Proxmox
description: 
published: true
date: 2023-06-14T18:20:02.397Z
tags: k8s, proxmox
editor: markdown
dateCreated: 2023-06-14T17:52:35.877Z
---

# Prérequis
## Un serveur hyperviseur
Plusieurs solutions sont disponibles, le plus simple reste le petit NUC boosté aux stéroïde. C'est vers cette solution que je me suis tourné !

Voici donc la bête que j'ai acheté spécialement pour ce POC : 

![poc_server.jpg](/Conteneurisation/poc_server.jpg)

> Disponible sur Amazon : https://amzn.to/3P9ySPp
{.is-info}

16 Threads et 32Go de RAM seront parfait pour faire un POC K8S, voir même de la prod dans un futur proche.
J'ai cependant profité du port SATA pour ajouter un SSD de 1To au NVME de 500Go déjà disponible.

## Installer Proxmox
Je ne vous guiderai pas dans ce tutoriel dans l'installation de Proxmox, mais vous pouvez retrouver toutes la documentation necessaire directement sur internet ou peut être dans un avenir proche dans cette catégorie Wiki-Tech : https://wiki-tech.io/Virtualisation#proxmox

## Configurer le SSD de 1To
Afin de bénéficier de la place disponible dans ce SSD SATA nouvellement ajouté à la machine, il fait le configurer dans Proxmox pour permettre son utilisation.

Pour cela, rendez vous dans la catégorie `Disk` de votre hyperviseur, et cliquer sur `Create Volume Group` :
![pve2_-_proxmox_virtual_environment_2023-06-14_19.56.48@2x.png](/Conteneurisation/pve2_-_proxmox_virtual_environment_2023-06-14_19.56.48@2x.png)

> Pensez à retenir le nom de votre volume, on va en avoir besoin par la suite.
{.is-warning}


> Votre disque est prêt à être utilisé ! 
{.is-success}

## Créer un utilisateur
Par la suite je vais utiliser Packer et Terraform pour déployer l'infra rapidement, pour cela, je vais créer un utilisateur dédié :
![pve2_-_proxmox_virtual_environment_2023-06-14_20.09.41@2x.png](/Conteneurisation/pve2_-_proxmox_virtual_environment_2023-06-14_20.09.41@2x.png)
![pve2_-_proxmox_virtual_environment_2023-06-14_20.11.16@2x.png](/Conteneurisation/pve2_-_proxmox_virtual_environment_2023-06-14_20.11.16@2x.png)

Et on lui ajoute les bonnes permissions :
![pve2_-_proxmox_virtual_environment_2023-06-14_20.12.57@2x.png](/Conteneurisation/pve2_-_proxmox_virtual_environment_2023-06-14_20.12.57@2x.png)
![pve2_-_proxmox_virtual_environment_2023-06-14_20.14.46@2x.png](/Conteneurisation/pve2_-_proxmox_virtual_environment_2023-06-14_20.14.46@2x.png)

## Créer d'un template Debian 12
Là aussi vous êtes libre de créer ce template par les moyens que vous souhaitez, dans mon cas je vais en profiter pour utiliser Packer avec l'aide de l'excellent projet de QJoly :

https://github.com/QJoly/Debian-Template-Proxmox
{.github}


