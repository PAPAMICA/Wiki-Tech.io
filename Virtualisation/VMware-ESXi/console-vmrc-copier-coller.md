---
title: Console VMRC
description: Comment activer le copier/coller
published: true
date: 2021-08-14T09:26:57.648Z
tags: vmrc, copier coller
editor: markdown
dateCreated: 2021-08-14T09:24:37.354Z
---

# Introduction
Le partage du presse-papier via la ***console VMRC*** est désactivé par défaut au sein d'ESXi pour des raisons de sécurité. Néanmoins, il y a deux possibilités d’activer ce partage, soit par VM soit directement sur l’hôte ESXi qui l’appliquera à l’ensemble des VM présentes sur celui-ci.

# Activation par VM

> La manipualtion doit être faite VM éteinte.
{.is-info}

Action => Modifier les paramètres :

![etape_1.png](/images/etape_1.png)

Il sera nécessaire d’aller par la suite dans les options de la VM (1), puis de développer la catégorie « Avancé » (2) pour choisir de modifier la configuration (3).

![etape_2.png](/images/etape_2.png)
![etape_3.png](/images/etape_3.png)

Une fois dans la nouvelle fenêtre qui s’ouvre, vous aurez la possibilité d’ajouter les deux options nécessaires pour activer le partage du copier/coller.

Pour ce faire, faites ***« Ajouter des paramètres de configuration »*** et renseigner comme nom et valeur les informations suivantes :

![etape_4.png](/images/etape_4.png)

|  Nom | Valeur  |
|:-:|:-:|
|isolation.tools.copy.disable   | FALSE  |  
|isolation.tools.paste.disable   | FALSE  |

Une fois fait, il vous faudra valider l’ensemble des fenêtres par le bouton ***« OK »***.
Au redémarrage de votre VM, le partage du presse-papier entre votre poste et la console sera actif.

# Activation sur l'hôte ESXi

Nous venons de voir comment activer le copier/coller pour une machine virtuelle. Toutefois, si vous avez plusieurs VM et que vous ne souhaitez pas faire la manipulation sur chaque VM les unes après les autres, la modification peut se faire directement sur l’hôte ESXi.

Dans un premier temps, il faudra faire l’activation du SSH sur l’ESXi.

Une fois en SSH sur votre ESXi, il vous faudra faire la modification du fichier suivant ***(pensez à faire une copie du fichier avant !)*** : 

`vi /etc/vmware/config`

Ajouter ensuite les lignes suivantes :

`vmx.fullpath = « /bin/vmx »`
`isolation.tools.copy.disable="FALSE"`
`isolation.tools.paste.disable="FALSE"`

Enregistrer votre fichier pour que les modifications soient prises en compte. Le redémarrage de l’ESXi n’est pas nécessaire, toutefois, ***celui des VM le sera pour que le partage du presse-papier soit actif.***

> Le copier/coller entre votre poste et les VM fonctionnera, toutefois, il ne s’agira que de pouvoir faire ***du partage de presse-papier contenant seulement du texte**.* Il n’est pas possible de faire du copier/coller de fichier entre un client et une VM.
{.is-info}

[*Source d'origine de la procédure*](https://www.tech2tech.fr/vmware-esxi-comment-activer-le-copier-coller-dans-la-console-vmrc/) | *Rédacteur Kevin ENGEL*