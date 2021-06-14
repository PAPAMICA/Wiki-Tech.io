---
title: Keypair
description: 
published: true
date: 2021-06-14T07:07:45.963Z
tags: 
editor: markdown
dateCreated: 2021-05-24T10:34:00.637Z
---

# Créer une keypair

Une paire de clés (ou keypair) est essentiellement une clé ssh vous permettant de vous connecter à votre machine virtuelle Linux.

## Utilisation de votre clé SSH existante

En supposant que votre clé publique ssh existante se trouve dans `~/.ssh/ssh_my_rsa_key.pub`

```plaintext
openstack keypair create --public-key ~/.ssh/ssh_my_rsa_key.pub mykeypair
+-------------+-------------------------------------------------+
| Field       | Value                                           |
+-------------+-------------------------------------------------+
| fingerprint | 53:c0:03:5f:27:6f:c2:66:b4:e4:f3:26:e0:f8:82:4a |
| name        | mykeypair                                       |
| user_id     | b1580497f51e4d10b9110c60c154562c                |
+-------------+-------------------------------------------------+
```

```plaintext
ssh -i ~/.ssh/ ~/.ssh/ssh_my_rsa_key *adminuser*@my-vm-name
```

## Création d'une keypair

Une nouvelle paire de clés peut également être créée pour être utilisée, par exemple avec un compte de service ou un deamon dédié.

```plaintext
openstack keypair create robotkey > ~/.ssh/robotkey
chmod 600 ~/.ssh/robotkey
```

La clé privée est enregistrée dans `~ /.ssh /robotkey` et peut ensuite être utilisée pour la création de VM (en utilisant l'argument `--key-pair` sur la ligne de commande) et pour ssh en utilisant :

```plaintext
ssh -i ~/.ssh/robotkey *adminuser*@my-vm-name
```