---
title: Infomaniak Public Cloud - Création d'un projet
description: Comment créer un projet et s'y connecter !
published: true
date: 2021-11-05T14:38:46.927Z
tags: openstack, infomaniak, public-cloud, cloud
editor: markdown
dateCreated: 2021-11-03T08:25:32.856Z
---

![plan_de_travail_1@4x.png](/images/cloud/infomaniak-public-cloud/plan_de_travail_1@4x.png =600x)
# Introduction
Nous allons voir dans cette procédure comment créer et se connecter à son projet Cloud Public.

# Création d'un projet
Un fois que vous avez commander votre Public Cloud, vous allez accéder au dashboard suivant sur lequel vous pourrez créer votre premier projet :
### Cliquez sur `Créer un projet`
![infomaniak-publiccloud_01.png](/images/cloud/infomaniak-public-cloud/1/infomaniak-publiccloud_01.png =1300x)
### Nommez votre projet
![infomaniak-publiccloud_02.png](/images/cloud/infomaniak-public-cloud/1/infomaniak-publiccloud_02.png =1300x)
### Créez un utilisateur pour ce projet
Vous pourrez créer d'autres utilisateurs par la suite ainsi que changer les mots de passe.
![infomaniak-publiccloud_03.png](/images/cloud/infomaniak-public-cloud/1/infomaniak-publiccloud_03.png =1300x)

> Votre projet est en cours de création !
{.is-success}

![infomaniak-publiccloud_04.png](/images/cloud/infomaniak-public-cloud/1/infomaniak-publiccloud_04.png =1300x)
### Après quelques minutes, vous receverez un mail vous annoncant la disponibilité de votre projet.
![infomaniak-publiccloud_05.png](/images/cloud/infomaniak-public-cloud/1/infomaniak-publiccloud_05.png =1000x)

# Gestion des utilisateurs
Vous pouvez modifier ou ajouter d'autres utilisateurs directement depuis le panel Infomaniak.
### Cliquez sur `Gérer` dans votre projet 
![infomaniak-publiccloud_06.png](/images/cloud/infomaniak-public-cloud/1/infomaniak-publiccloud_06.png =1300x)
### Sur cette page vous pourrez apporter les modifications que vous souhaitez
![infomaniak-publiccloud_07.png](/images/cloud/infomaniak-public-cloud/1/infomaniak-publiccloud_07.png =1300x)

# Connexion au projet avec Horizon
Horizon est une interface web vous permettant de créer et gérer vos instances, vos containers etc...
### Cliquez sur le nom de votre projet ou sur `Ouvrir le dashboard OpenStack`
![infomaniak-publiccloud_08.png](/images/cloud/infomaniak-public-cloud/1/infomaniak-publiccloud_08.png =1300x)
### Connectez vous avec les identifiants précédemment créés
![infomaniak-publiccloud_09.png](/images/cloud/infomaniak-public-cloud/1/infomaniak-publiccloud_09.png =1300x)

> Bienvenue sur le dashboard Openstack de votre projet !
{.is-success}

![infomaniak-publiccloud_10.png](/images/cloud/infomaniak-public-cloud/1/infomaniak-publiccloud_10.png =1300x)

# Connexion au projet en CLI
Vous pouvez utiliser l'API d'OpenStack ainsi que son client pour gérer votre projet directement depuis votre terminal.
## Installer le client OpenStack
Vous pouvez suivre le tutoriel suivant : 
- [🌀 Installer le client *Linux, MacOS et Windows*](/Cloud/OpenStack/Installation)
{.links-list}
## Récupérer les identifiants
### Dans la liste des utilisateurs, vous pouvez télécharger le `fichier OpenStack`
![infomaniak-publiccloud_11.png](/images/cloud/infomaniak-public-cloud/1/infomaniak-publiccloud_11.png =1300x)

Votre fichier ressemblera à ca :
```bash
export OS_AUTH_URL=https://api.pub1.infomaniak.cloud/identity/v3
export OS_PROJECT_NAME=PCP-U2CAZJ4
export OS_PROJECT_DOMAIN_NAME=default
export OS_USERNAME=PCU-U2CAZJ4
export OS_USER_DOMAIN_NAME=default
export OS_PROJECT_ID=7368f02b559648d0a9ff15bff29b464f
export OS_IDENTITY_API_VERSION=3
export OS_INTERFACE=public
export OS_REGION_NAME=dc3-a
# To avoid being prompted for your password each time,
# write your password below and uncomment the line
#OS_PASSWORD='your password goes here'
[ -z "$OS_PASSWORD" ] && read -e -p "Please enter your OpenStack Password for project $OS_PROJECT_NAME as user $OS_USERNAME: " OS_PASSWORD
export OS_PASSWORD
```
Comme écrit dans le fichier en question, si vous souhaitez ne pas avoir à rentrer votre mot de passe à chaque fois, vous pouvez modifier votre fichier comme ceci :
```bash
export OS_AUTH_URL=https://api.pub1.infomaniak.cloud/identity/v3
export OS_PROJECT_NAME=PCP-U2CAZJ4
export OS_PROJECT_DOMAIN_NAME=default
export OS_USERNAME=PCU-U2CAZJ4
export OS_USER_DOMAIN_NAME=default
export OS_PROJECT_ID=7368f02b559648d0a9ff15bff29b464f
export OS_IDENTITY_API_VERSION=3
export OS_INTERFACE=public
export OS_REGION_NAME=dc3-a
export OS_PASSWORD='<mot_de_passe>'
```
> Pensez à remplacer `<mot_de_passe>`par votre mot de passe évidemment.
{.is-warning}

### Pour vous connecter, vous devez sourcer votre fichier :
```bash
source <votre_fichier>
```

### Vous pouvez vérifier que vous êtes bien connecter en listant les flavors disponibles par exemple :
```bash
openstack flavor list
```
Si tout se passe bien, vous devriez voir la liste des flavors :
```bash
+--------------------------------------+------------------------+-------+------+-----------+-------+-----------+
| ID                                   | Name                   |   RAM | Disk | Ephemeral | VCPUs | Is Public |
+--------------------------------------+------------------------+-------+------+-----------+-------+-----------+
| 019bba95-4b83-4f27-b04a-bd52f207f0e6 | a16-ram32-disk50-perf1 | 32768 |   50 |         0 |    16 | True      |
| 0dc5bd7c-e85a-498e-88a3-b950ea5ab106 | a1-ram2-disk80-perf1   |  2048 |   80 |         0 |     1 | True      |
| 12c044f0-842f-48d4-9553-700095cb5153 | a2-ram4-disk0          |  4096 |    0 |         0 |     2 | True      |
| 21aad244-a330-4e79-ba80-4c057cf742f9 | a1-ram2-disk20-perf1   |  2048 |   20 |         0 |     1 | True      |
| 2b072d6a-92ff-495a-9528-835c5e360123 | a2-ram4-disk50-perf1   |  4096 |   50 |         0 |     2 | True      |
| 366010f9-67e0-4c83-bae0-e3bdec3c2c5d | a1-ram2-disk50-perf1   |  2048 |   50 |         0 |     1 | True      |
| 3e0fd116-1e94-464d-95af-7f12eca50744 | a8-ram16-disk0         | 16384 |    0 |         0 |     8 | True      |
| 478f6e28-36a7-4e96-93c1-9bc3a5bc20b5 | a4-ram8-disk50-perf1   |  8192 |   50 |         0 |     4 | True      |
| 47a2e96f-364e-4191-a273-4e3bf8cff791 | a12-ram24-disk50-perf1 | 24576 |   50 |         0 |    12 | True      |
| 60298864-77b4-4058-9861-50fea072c5fd | a2-ram4-disk80-perf1   |  4096 |   80 |         0 |     2 | True      |
| 6d0e3553-fdf7-4008-ace8-39169ce127a7 | a8-ram16-disk50-perf1  | 16384 |   50 |         0 |     8 | True      |
| 7918af3e-aa2a-4aa4-976d-9056490a4654 | a4-ram8-disk20-perf1   |  8192 |   20 |         0 |     4 | True      |
| 8e5deff9-d4e9-4f6f-952c-10875acecda8 | a4-ram8-disk0          |  8192 |    0 |         0 |     4 | True      |
| a1d6e394-e4db-486b-8091-5d95cfbf3952 | a12-ram24-disk20-perf1 | 24576 |   20 |         0 |    12 | True      |
| a35c6646-0f3c-464b-b50d-2a76cad0bd7b | a16-ram32-disk20-perf1 | 32768 |   20 |         0 |    16 | True      |
| b6b7baeb-2328-48c9-8543-88cccec8ec4b | a2-ram4-disk20-perf1   |  4096 |   20 |         0 |     2 | True      |
| ca6bd4f0-37d5-47f4-9edc-2cee017ac248 | a12-ram24-disk0        | 24576 |    0 |         0 |    12 | True      |
| cd0483a8-ca2a-466b-89b2-f8d0d005408a | a8-ram16-disk20-perf1  | 16384 |   20 |         0 |     8 | True      |
| d0fb6907-dde8-4bda-92c3-b84feedf171e | a16-ram32-disk80-perf1 | 32768 |   80 |         0 |    16 | True      |
| d120e7de-01a3-4aca-b7e6-0fae9e9e7937 | a4-ram8-disk80-perf1   |  8192 |   80 |         0 |     4 | True      |
| d21c5591-0b1d-45bf-9b0e-4a6c54c82fcf | a12-ram24-disk80-perf1 | 24576 |   80 |         0 |    12 | True      |
| db13a284-48e7-4e28-89fa-1191c0e1508e | a8-ram16-disk80-perf1  | 16384 |   80 |         0 |     8 | True      |
| e36581a7-e4a0-42b8-b91c-1a44747f0aff | a1-ram2-disk0          |  2048 |    0 |         0 |     1 | True      |
| e9b77f56-9682-4874-a4ec-f092891fdc4a | a16-ram32-disk0        | 32768 |    0 |         0 |    16 | True      |
+--------------------------------------+------------------------+-------+------+-----------+-------+-----------+
```

> Vous pouvez utiliser votre projet directement depuis votre terminal !
{.is-success}
# Créer un identifiant d'application
Afin d'utiliser le Public Cloud d'Infomaniak avec des applications externes (Terraform par exemple) vous aurez besoin d'un **identifiant d'application**

### Rendez vous dans la section `Identifiants d'application` dans le dashboard Horizon et cliquez sur `Créer un identifiant d'application` :
![infomaniak-publiccloud_12.png](/images/cloud/infomaniak-public-cloud/1/infomaniak-publiccloud_12.png =1300x)
### Rentrez les informations et selectionnez les droits que vous souhaitez :
![infomaniak-publiccloud_13.png](/images/cloud/infomaniak-public-cloud/1/infomaniak-publiccloud_13.png =600x)
### Enregistrez les identifiants ou téléchargez les :
![infomaniak-publiccloud_14.png](/images/cloud/infomaniak-public-cloud/1/infomaniak-publiccloud_14.png =600x)