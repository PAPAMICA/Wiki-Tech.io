---
title: Configurer son environnement
description: 
published: true
date: 2021-06-14T07:05:05.646Z
tags: 
editor: markdown
dateCreated: 2021-05-24T10:33:51.945Z
---

# Configurer son environnement

Les clients OpenStack trouvent les services à utiliser à l'aide de variables d'environnement. Ceux-ci sont généralement stockés dans un fichier shell appelé openrc. Les variables seront différentes selon le projet que vous utilisez. Les détails de ces variables d'environnement se trouvent dans la documentation d'OpenStack.

Afin d'éviter de définir ces variables d'environnement manuellement, vous pouvez récupérer le fichier directement depuis le dashboard.

## Téléchargement depuis le dashboard

Accédez au tableau de bord OpenStack, connectez-vous et vous pourrez télécharger un fichier openrc comme cela :

-   Sélectionez "Project" dans le menu de gauche (juste en dessous du logo "openstack")
-   Rendez-vous dans l'onglet “API Access”
-   Cliquez sur le bouton "Download OpenStack RC File" sur la droitet et cliquez sur “OpenStack RC File”

Cela téléchargera un fichier .sh via votre navigateur qui peut être utilisé pour l'authentification. Le script doit être chargé de la manière suivante à partir d'un terminal Linux :

Avec Bash :

```bash
. openrc.sh
```

Avec Zsh :

```bash
source openrc.sh
```

> Si vous souhaitez télécharger un fichier openrc pour un autre projet, vous devez basculer le projet de la liste déroulante juste à droite vers le logo "openstack" afin de "l'activer" dans l'interface Web et ainsi télécharger le bon fichier pour le projet souhaité.
{.is-info}

Pour “décharger” ces variables d'environnements, vous pouvez soit fermer votre invite de commande, soit les “décharger” avec cette commande :

```bash
 unset OS_PASSWORD
```

## Gestion des projets

Un projet est un groupe de zéro ou plusieurs utilisateurs. Dans Compute, un projet possède des machines virtuelles. Dans Object Storage, un projet possède des conteneurs. Les utilisateurs peuvent être associés à plusieurs projets. Chaque association projet et utilisateur peut être associée à un rôle.

### Lister les projets

Lister tous les projets avec leur ID, leur nom et s'ils sont activés ou désactivés avec `openstack project list` :

```bash
openstack project list
+----------------------------------+--------------------+
| ID                               | Name               |
+----------------------------------+--------------------+
| f7ac731cc11f40efbc03a9f9e1d1d21f | admin              |
| c150ab41f0d9443f8874e32e725a4cc8 | alt_demo           |
| a9debfe41a6d4d09a677da737b907d5e | demo               |
| 9208739195a34c628c58c95d157917d7 | invisible_to_admin |
| 3943a53dc92a49b2827fae94363851e1 | service            |
| 80cab5e1f02045abad92a2864cfd76cb | test_project       |
+----------------------------------+--------------------+
```

### Créer un projet

Créer un projet “new-project” avec `openstack project create` :

```bash
openstack project create --description 'mon nouveau projet' new-project --domain default
+-------------+----------------------------------+
| Field       | Value                            |
+-------------+----------------------------------+
| description | mon nouveau projet               |
| domain_id   | e601210181f54843b51b3edff41d4980 |
| enabled     | True                             |
| id          | 1a4a0618b306462c9830f876b0bd6af2 |
| is_domain   | False                            |
| name        | new-project                      |
| parent_id   | e601210181f54843b51b3edff41d4980 |
+-------------+----------------------------------+
```

### Mettre à jour un projet

Spécifiez l'ID de projet pour mettre à jour un projet. Vous pouvez mettre à jour le nom, la description et l'état d'activation d'un projet avec `openstack project set` :

-   Désactiver temporairement un projet :

```bash
openstack project set PROJECT_ID --disable
```

-   Activé un projet désactivé :

```bash
openstack project set PROJECT_ID --enable
```

-   Changer le nom d'un projet :

```bash
openstack project set PROJECT_ID --name project-new
```

### Voir les informations d'un projet :

Pour lister les informations ou voir les changements effectués avec `openstack project show` :

```bash
openstack project show PROJECT_ID
+-------------+----------------------------------+
| Field       | Value                            |
+-------------+----------------------------------+
| description | mon nouveau projet               |
| enabled     | True                             |
| id          | 0b0b995694234521bf93c792ed44247f |
| name        | new-project                      |
| properties  |                                  |
+-------------+----------------------------------+
```

### Supprimer un projet

Il faut utiliser l'ID avec la commande `openstack project delete` :

```bash
openstack project delete PROJECT_ID
```

## Gestion des utilisateurs

### Lister les utilisateurs

La commande pour lister les utilisateurs est simplement : `openstack user list`

```bash
openstack user list
+----------------------------------+----------+
| ID                               | Name     |
+----------------------------------+----------+
| 352b37f5c89144d4ad0534139266d51f | admin    |
| 86c0de739bcb4802b8dc786921355813 | demo     |
| 32ec34aae8ea432e8af560a1cec0e881 | glance   |
| 7047fcb7908e420cb36e13bbd72c972c | nova     |
+----------------------------------+----------+
```

### Créer un utilisateur

Pour créer un utilisateur, vous devez spécifier un nom avec la commande `openstack user create`. Vous pouvez éventuellement spécifier un ID de projet, un mot de passe et une adresse e-mail. 

> **Attention :** Il est recommandé d'inclure l'ID de projet et le mot de passe car l'utilisateur ne peut pas se connecter au tableau de bord sans ces informations.

```bash
openstack user create --project new-project --password PASSWORD papamica
+------------+----------------------------------+
| Field      | Value                            |
+------------+----------------------------------+
| email      | None                             |
| enabled    | True                             |
| id         | 6322872d9c7e445dbbb49c1f9ca28adc |
| name       | papamica                         |
| project_id | 0b0b995694234521bf93c792ed44247f |
| username   | papamica                         |
+------------+----------------------------------+
```

Modifier un utilisateur

Vous pouvez mettre à jour le nom, l'adresse e-mail et l'état d'activation d'un utilisateur avec `openstack user set`:

-   Pour désactiver temporairement un utilisateur :

```bash
openstack user set USER_NAME --disable
```

> **Remarque :** Si vous désactivez un compte utilisateur, l'utilisateur ne peut pas se connecter au tableau de bord. Cependant, les données du compte utilisateur sont conservées, vous pouvez donc réactiver l'utilisateur à tout moment.

-   Pour réactiver un utilisateur :

```bash
openstack user set USER_NAME --enable
```

-   Pour changer le nom, la description ou encore l'email d'un utilisateur :

```bash
openstack user set USER_NAME --name user-new --email new-user@example.com
User has been updated.
```

### Supprimer un utilisateur

Rien de plus compliqué et toujours avec la même logique de commande : `openstack user delete`

```bash
openstack user delete USER_NAME
```

## Gestion des rôles

### Lister les rôles

`openstack role list` permet de lister les rôles déjà disponibles :

```bash
openstack role list
+----------------------------------+---------------+
| ID                               | Name          |
+----------------------------------+---------------+
| 71ccc37d41c8491c975ae72676db687f | Member        |
| 149f50a1fe684bfa88dae76a48d26ef7 | ResellerAdmin |
| 9fe2ff9ee4384b1894a90878d3e92bab | _member_      |
| 6ecf391421604da985db2f141e46a7c8 | admin         |
| deb4fffd123c4d02a907c2c74559dccf | anotherrole   |
+----------------------------------+---------------+
```

### Créer un rôle

> Les utilisateurs peuvent être membres de plusieurs projets. Pour affecter des utilisateurs à plusieurs projets, définissez un rôle et attribuez ce rôle à des projets.
{.is-info}

Pour créer un nouveau rôle “test” avec `openstack role create` :

```bash
openstack role create test
+-----------+----------------------------------+
| Field     | Value                            |
+-----------+----------------------------------+
| domain_id | None                             |
| id        | a34425c884c74c8881496dc2c2e84ffc |
| name      | test                             |
+-----------+----------------------------------+
```

### Assigner un rôle

> Pour affecter un utilisateur à un projet, vous devez attribuer le rôle à une paire utilisateur-projet. Pour ce faire, vous avez besoin des ID d'utilisateur, de rôle et de projet.
{.is-info}

Il va donc falloir récupérer les info suivante :

-   ID de l'utilisateur : `openstack user list`
-   ID du rôles : `openstack role list`
-   ID du projet : `openstack project list`

Ajouter le rôle *admin* à l'utilisateur *papamica* et son projet *new-project* avec `openstack role add` :

```bash
openstack role add --user papamica --project new-project admin
```

Pour vérifier que le rôle a bien été affecté :

```bash
openstack role assignment list --user papamica --project new-project --names
+----------------------------------+-------------+----------------+----------+
| ID                               | Name        | Project        | User     |
+----------------------------------+-------------+----------------+----------+
| a34425c884c74c8881496dc2c2e84ffc | admin       | new-project    | papamica |
| 04a7e3192c0745a2b1e3d2baf5a3ee0f | Member      | new-project    | papamica |
| 62bcf3e27eef4f648eb72d1f9920f6e5 | anotherrole | new-project    | papamica |
+----------------------------------+-------------+----------------+----------+
```

### Voir les détails d'un rôle

Vous pouvez lister tous les détails d'un rôle spécifique avec `openstack role show` :

```bash
openstack role show test
+-----------+----------------------------------+
| Field     | Value                            |
+-----------+----------------------------------+
| domain_id | None                             |
| id        | a34425c884c74c8881496dc2c2e84ffc |
| name      | test                             |
+-----------+----------------------------------+
```

### Enlever un rôle

La commande reste `openstack role remove` avec l'utilisateur et le projet :

```bash
openstack role remove --user papamica --project new-project test
```

Pour vérifier que le rôle a bien été enlevé, il suffit de lister les rôles de cet utilisateur/projet et constater qu'il n'est plus présent.