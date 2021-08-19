---
title: Mise à jour d'un hôte ESXi
description: Procédure indiquant les étapes de mise à jour d'un hôte ESXi
published: true
date: 2021-06-06T06:48:52.009Z
tags: update, vmware, esxi, mise à jour
editor: markdown
dateCreated: 2021-05-24T10:37:44.048Z
---


# Préparation

## Disponibilité

Avant de mettre à jour notre ESXi, il nous faudra tout d’abord faire la vérification de la version que nous avons en production, puis aller consulter les mises à jour disponibles pour cette version. Pour ce faire, lorsque nous sommes connectés à notre ESXi, sur la page d’accueil nous avons le ***numéro de version ainsi que de build*** qui est indiqué dans le menu ***« Aide »*** puis ***« À propos de »*** situer en haut à droite.

![](/images/check_maj.png)

Maintenant que nous avons cette information, nous devons nous rendre sur le site de VMware à [***cette adresse***](https://my.vmware.com/group/vmware/patch#search).  
Et faire la recherche de disponibilité de mise à jour et le téléchargement de cette dernière.

> Il vous faudra obligatoirement un compte VMware afin d’avoir accès aux recherches et téléchargements des mises à jour.
{.is-warning}


## Téléchargement

Plusieurs solutions sont possibles pour mettre à disposition la mise à jour sur l’ESXI, soit via un accès FTP ou également par le dépôt directement dans un datastore *(solution que nous utilisons ici)*.

![](/images/add_update_datastore.png)

## Préparation de l’ESXi

Nous allons maintenant faire la dernière étape de préparation pour la réalisation de la mise à jour de notre hyperviseur.  
La mise à jour se réalise via une ligne de commande par SSH. Nous devons donc faire l'activation de l'accès SSH sur notre ESxi.

![](/images/update_esxi_ssh.png)

Et il nous reste plus qu'à mettre notre hôte en mode maintenance.

![](/images/update_esxi_maintmode.png)

> Lors de la mise en mode maintenance, l’ESXi va mettre en pause vos VM.*  
> *Néanmoins, je vous préconise de faire l’arrêt complet de vos différentes VM avant de passer en mode maintenance.
{.is-warning}


> Je ne l’aborde pas ici, car pour moi il s’agit d’une évidence, mais je le rappelle quand même par précaution : **avant de faire la mise à jour de votre ESXi assurez-vous d’avoir une sauvegarde (fonctionnelle) de votre hyperviseur.**
{.is-info}


# **Mise à jour de l'ESXi**

Il est maintenant temps de sortir votre client SSH favori *(Putty dans mon cas)* et de ce connecter à notre ESXi.  
Une fois sur celui-ci, nous devons lui envoyer la commande suivante :

```
esxcli software vib install -d /vmfs/volumes/Datastrore-3-HDD/patchs/VMware-ESXi-7.0U1d-17551050-depot.zip
```

> Il vous faudra bien sûr adapter la commande selon l’emplacement de votre fichier.
{.is-warning}


Si la mise à jour se passe correctement, vous obtiendrez le message suivant :

```
Message: The update completed successfully, but the system needs to be rebooted for the changes to be effective. 
Reboot Required: true
```

Comme nous l’indique l’ESXi, il a besoin d’un redémarrage afin d’appliquer la mise à jour.  
Il nous suffira donc de faire la commande ci-dessous et d’attendre le redémarrage.

```
reboot
```

Une fois le redémarrage fait, vous pouvez désactiver le mode de maintenance de votre ESxi et faire la vérification de la bonne application de la mise à jour depuis le menu ***« À propos de »*** que nous avons vu au début de cet article.


---
[*Source d'origine de la procédure*](https://www.tech2tech.fr/vmware-comment-mettre-a-jour-esxi/) | *Rédacteur Kevin ENGEL*