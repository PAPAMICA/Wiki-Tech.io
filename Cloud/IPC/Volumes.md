---
title: Infomaniak Public Cloud - Les volumes
description: Créer et gérer les volumes (stockage bloc)
published: true
date: 2021-11-09T10:11:38.120Z
tags: openstack, infomaniak, public-cloud, cloud, ipc, volume, ceph
editor: markdown
dateCreated: 2021-11-09T09:28:55.603Z
---

![plan_de_travail_1@4x.png](/images/cloud/infomaniak-public-cloud/plan_de_travail_1@4x.png =600x)

# Introduction
Les volumes Openstack sont du **stockage block** qui utilise le **service Cinder** qui repose sur différents backends (Ceph, LVM, etc...)
Les volumes permettent d'**ajouter un "disque dur" supplémentaire** à une instance.
Il ne peux etre attaché qu'à **une seule instance à la fois**.
Il peux être **déplacer** d'une instance à une autre.
Pour être exploité, il dois être **formaté et monté** dans le système d'exploitation de l'instance.

# Horizon
## Créer un volume
### Rendez vous dans la catégorie `Volumes` et cliquez sur `Créer un Volume`
![infomaniak-publiccloud_9_1.png](/images/cloud/infomaniak-public-cloud/9/infomaniak-publiccloud_9_1.png =1300x)
### Nommez votre volume, selectionnez la taille et cliquez sur `Créer un Volume`
![infomaniak-publiccloud_9_2.png](/images/cloud/infomaniak-public-cloud/9/infomaniak-publiccloud_9_2.png =600x)


## Connecter un volume à une instance
### Rendez vous dans la catégorie `Instances`, choisissez votre instance et cliquez sur `Attacher un Volume`
![infomaniak-publiccloud_9_3.png](/images/cloud/infomaniak-public-cloud/9/infomaniak-publiccloud_9_3.png =1300x)

### Selectionnez votre volume et cliquez sur `Attacher un Volume`
![infomaniak-publiccloud_9_4.png](/images/cloud/infomaniak-public-cloud/9/infomaniak-publiccloud_9_4.png =600x)


# CLI
## Créer un volume
## Connecter un volume à une instance

## Lister les volumes
## Afficher un volume
## Supprimer un volume