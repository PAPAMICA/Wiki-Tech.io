---
title: Images
description: 
published: true
date: 2021-06-14T07:09:22.451Z
tags: 
editor: markdown
dateCreated: 2021-05-24T10:33:56.342Z
---

# Les images

Les images sont les systèmes d'exploitations que vous utilisez lors de la création de la machine virtuelle. 

> Elles sont comparables aux ISO mais dois forcément être au format `qcow` et `raw`.
{.is-warning}


## Lister les images

La sélection disponible peut être affichée en utilisant : `openstack image list`

```plaintext
openstack image list
+--------------------------------------+-----------------------------------------------+--------+
| ID                                   | Name                                          | Status |
+--------------------------------------+-----------------------------------------------+--------+
| 69112d12-0acb-4321-bbc6-f2d493ab6be3 | CentOS 8 GenericCloud                         | active |
| 77ce9a1a-4c03-43d7-9f87-b447a39103bb | debian-10-genericcloud-amd64-20210208-542.raw | active |
| 735a5c16-56f0-4c15-80e7-49056dbc4f71 | debian-10.7.4-20210108-openstack-amd64.raw    | active |
| 0c857837-f852-44dc-b986-bcc488bf7b70 | debian-10.8-openstack-amd64.raw               | active |
| 49f425ed-bf79-46ab-8cf3-44935d9d831e | debian-10.8.0-openstack-amd64.raw             | active |
| daf43e02-f59e-45bb-8d63-436094d3f360 | debian-buster-202102191137-amd64.raw          | active |
+--------------------------------------+-----------------------------------------------+--------+
```

Pour afficher les informations à propose d'une image, vous pouvez utiliser la commande `openstack image show <IMAGE_NAME>` 

```plaintext
openstack image show debian-10.8-openstack-amd64.raw
+------------------+------------------------------------------------------+
| Field            | Value                                                |
+------------------+------------------------------------------------------+
| checksum         | ee1eca47dc88f4879d8a229cc70a07c6                     |
| container_format | ami                                                  |
| created_at       | 2016-08-11T15:07:26Z                                 |
| disk_format      | ami                                                  |
| file             | /v2/images/d07831df-edc3-4817-9881-89141f9134c3/file |
| id               | d07831df-edc3-4817-9881-89141f9134c3                 |
| min_disk         | 0                                                    |
| min_ram          | 0                                                    |
| name             | debian-10.8-openstack-amd64.raw                      |
| owner            | d88310717a8e4ebcae84ed075f82c51e                     |
| protected        | False                                                |
| schema           | /v2/schemas/image                                    |
| size             | 13287936                                             |
| status           | active                                               |
| tags             |                                                      |
| updated_at       | 2021-04-11T15:20:02Z                                 |
| virtual_size     | None                                                 |
| visibility       | private                                              |
+------------------+------------------------------------------------------+
```

Il est aussi possible de filtrer la liste des images avec `grep` :

```plaintext
openstack image list | grep 'debian'
| 77ce9a1a-4c03-43d7-9f87-b447a39103bb | debian-10-genericcloud-amd64-20210208-542.raw | active |
| 735a5c16-56f0-4c15-80e7-49056dbc4f71 | debian-10.7.4-20210108-openstack-amd64.raw    | active |
| 0c857837-f852-44dc-b986-bcc488bf7b70 | debian-10.8-openstack-amd64.raw               | active |
| 49f425ed-bf79-46ab-8cf3-44935d9d831e | debian-10.8.0-openstack-amd64.raw             | active |
| daf43e02-f59e-45bb-8d63-436094d3f360 | debian-buster-202102191137-amd64.raw          | active |
```

## Créer une image

Pour créer une image il faut au préalable télécharger l'image du système d'exploitation au format `qcow` ou `raw`. Une fois l'image dans le dossier de votre serveur, vous pouvez la créer dans OpenStack avec `openstack image create` et les arguments suivants :

```plaintext
openstack image create --disk-format=<DISK_FORMAT> --container-format=<CONTAINER_FORMAT> --file=<IMAGE_FILE> --public "<IMAGE_NAME>"
```

Par exemple pour créer une image Debian 10.8 :

```plaintext
openstack image create --disk-format=raw --container-format=bare --file=debian-10.8-openstack-amd64.raw  --public "Debian 10.8"
```

## Supprimer une image

Pour cela il suffit d'utiliser la commande `openstack image delete <IMAGE_ID>`

```plaintext
openstack image delete 0c857837-f852-44dc-b986-bcc488bf7b70
```