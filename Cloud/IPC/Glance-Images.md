---
title: Infomaniak Public Cloud - Glance : La gestion des images
description: Créer et gérer ses images systèmes.
published: true
date: 2021-11-04T12:31:19.327Z
tags: openstack, infomaniak, public-cloud, cloud, ipc
editor: markdown
dateCreated: 2021-11-04T09:15:22.077Z
---

![plan_de_travail_1@4x.png](/images/cloud/infomaniak-public-cloud/plan_de_travail_1@4x.png =600x)

# Introduction
Sur le Cloud Public d'Infomaniak, vous pouvez utiliser les **images mises à disposition par Infomaniak** directement parmis une liste non exaustive :
- Debian
- Ubuntu
- RancherOS
- openSUSE
- FreeBSD
- Fedora Core OS
- CentOS
- Arch Linux
- CirrOS
- etc...

Ou vous pouvez directement **envoyer vos propres images** et les rendres publiques ou les garder privées.
Plusieurs formats sont supportés : **ISO**, **PLOOP**, **QCOW2**, **RAW**, **VDI**, **VHD**, **VMDK**, **AKI**, **AMI** et **ARI**.

# Horizon
## Télécharger une image
### Rendez vous dans la catégorie `Images` et cliquez sur `+ Créer une image`
![infomaniak-publiccloud_3_1.png](/images/cloud/infomaniak-public-cloud/3/infomaniak-publiccloud_3_1.png =1300x)

### Nommez votre image, selectionnez votre fichier, son format et sa visibilité puis cliquez sur `Créer une image`
![infomaniak-publiccloud_3_2.png](/images/cloud/infomaniak-public-cloud/3/infomaniak-publiccloud_3_2.png =1300x)

> Au bout de quelques minutes, votre image est disponible !
>![infomaniak-publiccloud_3_3.png](/images/cloud/infomaniak-public-cloud/3/infomaniak-publiccloud_3_3.png =1300x)
{.is-success}

## Supprimer une image
### Une fois votre image selectionnée, cliquez sur `Supprimer l'image`
![infomaniak-publiccloud_3_4.png](/images/cloud/infomaniak-public-cloud/3/infomaniak-publiccloud_3_4.png =1300x)

# CLI
## Télécharger une image
```bash
openstack image create
    [--disk-format <DISK_FORMAT>]
    [--file <FILE> | --volume <VOLUME>]
    [--force]
    [--protected | --unprotected]
    [--public | --private | --community | --shared]
    <IMAGE_NAME>
```
> **--disk-format *DISK_FORMAT*** 
>- Format de l'image (ami, ari, aki, vhd, vmdk, raw, qcow2, vhdx, vdi, iso, ploop). Par défaut : raw
>
> **--file *FILE***
> - Télécharger une image à partir d'un fichier local
>
> **--volume *VOLUME***
> - Créer une image à partir d'un volume
>
> **--force**
> - Forcer la création d'image si le volume est en cours d'utilisation (uniquement avec --volume)
>
> **--protected | --unprotected**
> - Empêcher la suppression de l'image
>
> **--public | --private | --community | --shared**
> - Régler l'accessibilité de l'image
>
> ***IMAGE_NAME***
> - Nom de l'image à créer
>
> 	.
{.is-info}

## Lister les images
```bash
openstack image list
    [--public | --private | --shared]
    [--name <NAME>]
    [--status <STATUS>]
```

> **--public | --private | --community | --shared**
> - Filtrer par accessibilité 
>
> **--name *NAME***
> - Filtrer par nom d'image
>
> **--status *STATUS***
> - Filtrer par status
>
> 	.
{.is-info}

## Afficher les informations de l'image
```bash
openstack image show <IMAGE>
```

> ***IMAGE***
> - Nom de l'image à afficher
>
> 	.
{.is-info}

## Supprimer une image
```bash
openstack image delete <IMAGE>
```

> ***IMAGE***
> - Nom de l'image à supprimer
>
> 	.
{.is-info}
