---
title: Flavors
description: 
published: true
date: 2021-06-14T07:08:17.028Z
tags: 
editor: markdown
dateCreated: 2021-05-24T10:33:54.158Z
---

# Les flavors

## Lister les flavors

Les flavors vous permettent de choisir la taille de votre machine virtuelle. La sélection disponible peut être affichée en utilisant : `openstack flavor list`

```plaintext
openstack flavor list
+--------------------------------------+---------------------------+------+------+-----------+-------+-----------+
| ID                                   | Name                      |  RAM | Disk | Ephemeral | VCPUs | Is Public |
+--------------------------------------+---------------------------+------+------+-----------+-------+-----------+
| 23c60b2b-6a1a-4870-ba41-ab1d6ce0c516 | cpu1-ram2-disk10          | 2048 |   10 |         0 |     1 | True      |
| 2655c1e3-a43a-4dea-a957-650517e8fa72 | cpu2-ram6-disk20_iops     | 6144 |   20 |         0 |     2 | True      |
| 450314a4-3575-45d5-88c2-41ca78201ebe | cpu2-ram6-disk60          | 6144 |   60 |         0 |     2 | True      |
| e5874e74-181c-4534-94db-ba035ded5dce | cpu2-ram6-disk20          | 6144 |   20 |         0 |     2 | True      |
| ef91c4c8-c28f-44b0-b7a0-3115652b52d1 | cpu2-ram6-disk40          | 6144 |   40 |         0 |     2 | True      |
| f2c62dc0-4b83-4208-868a-fbc4dbc2ff3a | cpu1-ram2-disk20          | 2048 |   20 |         0 |     1 | True      |
+--------------------------------------+---------------------------+------+------+-----------+-------+-----------+
```

Ils se déclinent en plusieurs versions offrant différents niveaux de performances. Le suffixe `_iops` derrière une saveur signifie une limite d'iops plus élevée.

|     |     |
| --- | --- |
| **Flavor** | **Performances maximales** |
| cpu2-ram6-disk20 | iops: 1k en lecture et écriture - net: 200MB/s |
| cpu2-ram6-disk20\_iops | iops: 8k en lecture et écriture - net: 200MB/s |
| cpu2-ram6-disk20\_perf | iops: 8k en lecture et écriture - net: 500MB/s |

Vous pouvez voir toutes les propriétés des flavors en utilisant la commande : `openstack flavor list --long`

```plaintext
openstack flavor list --long
+--------------------------------------+---------------------------+------+------+-----------+-------+-----------+------+-------------+--------------------------------------------------------------------+
| ID                                   | Name                      |  RAM | Disk | Ephemeral | VCPUs | Is Public | Swap | RXTX Factor | Properties                                                         |
+--------------------------------------+---------------------------+------+------+-----------+-------+-----------+------+-------------+--------------------------------------------------------------------+
[...]
| e5874e74-181c-4534-94db-ba035ded5dce | cpu2-ram6-disk20_standard | 6144 |   20 |         0 |     2 | True      |      |         1.0 | disk_total_iops_sec='1000', quota:disk_write_bytes_sec='209715200' |
| 450314a4-3575-45d5-88c2-41ca78201ebe | cpu2-ram6-disk20_iops     | 6144 |   20 |         0 |     2 | True      |      |         1.0 | disk_total_iops_sec='8000', quota:disk_write_bytes_sec='209715200' |
| f2c62dc0-4b83-4208-868a-fbc4dbc2ff3a | cpu2-ram6-disk20_perf     | 6144 |   20 |         0 |     2 | True      |      |         1.0 | disk_total_iops_sec='8000', quota:disk_write_bytes_sec='524288000' |
[...]
+--------------------------------------+---------------------------+------+------+-----------+-------+-----------+------+-------------+--------------------------------------------------------------------+
```

## Créer un flavor

Pour créer un flavor il faut utiliser la commande `openstack flavor create` avec les arguments suivants :

```plaintext
openstack flavor create FLAVOR_NAME --id FLAVOR_ID --ram RAM_IN_MB --disk ROOT_DISK_IN_GB --vcpus NUMBER_OF_VCPUS
```

Par exemple pour créer un flavor pour une petite machine virtuelle :

```plaintext
openstack flavor create cpu1-ram2-disk10 --id auto --ram 2048 --disk 10 --vcpus 1
```

## Supprimer un flavor

Pour cela il suffit d'utiliser la commande `openstack flavor delete <FLAVOR_ID>`

```plaintext
openstack flavor delete 23c60b2b-6a1a-4870-ba41-ab1d6ce0c516
```