---
title: Infomaniak Public Cloud - Gestion des paires de clés
description: Créer et gérer les paires de clés pour préparer la connexion SSH
published: true
date: 2021-11-05T14:42:45.554Z
tags: ssh, infomaniak, public-cloud, cloud, ipc
editor: markdown
dateCreated: 2021-11-04T12:51:34.885Z
---

![plan_de_travail_1@4x.png](/images/cloud/infomaniak-public-cloud/plan_de_travail_1@4x.png =600x)
# Introduction
Les paires de clés (Keypair en anglais) sont une méthode d'authentification SSH.
Face à la faiblesse de l'authentification par mot de passe, l'**authentification par clé se révèle être très efficace**.

La clé permet de garantir à un système qu'un utilisateur est bien celui qu'il prétend être... en deux mots : « Je jure et je prouve que c'est bien moi ».

L'authentification par clé fonctionne grâce à **3 composants** :
 - Une **clé publique** : elle sera exportée sur chaque hôte sur lequel on souhaite pouvoir se connecter
 - Une **clé privée** : elle permet de prouver son identité aux serveurs
 - Une **passphrase** : Permet de sécuriser la clé privée (notons la subtilité, passphrase et pas password... donc « phrase de passe » et non pas « mot de passe »).

La sécurité est vraiment accrue car la passphrase seule ne sert à rien sans la clé privée, et vice-versa. 
# Horizon
## Créer une paire de clé
### Rendez vous dans la catégorie `Paires de clés` et cliquez sur `+ Créer une paire de clés`
![infomaniak-publiccloud_4_1.png](/images/cloud/infomaniak-public-cloud/4/infomaniak-publiccloud_4_1.png =1300x)
### Renseignez un nom et le type
![infomaniak-publiccloud_4_2.png](/images/cloud/infomaniak-public-cloud/4/infomaniak-publiccloud_4_2.png =1300x)
### Téléchargez le fichier `.pem`
![infomaniak-publiccloud_4_3.png](/images/cloud/infomaniak-public-cloud/4/infomaniak-publiccloud_4_3.png =600x)

> Votre paire de clé est créée !
> ![infomaniak-publiccloud_4_4.png](/images/cloud/infomaniak-public-cloud/4/infomaniak-publiccloud_4_4.png =1300x)
{.is-success}


## Importer une clé publique
### Rendez vous dans la catégorie `Paires de clés` et cliquez sur ` Importer une clé publique`
![infomaniak-publiccloud_4_6.png](/images/cloud/infomaniak-public-cloud/4/infomaniak-publiccloud_4_6.png =1300x)
### Nommez la clé, choissisez son type et coller votre clé publique
![infomaniak-publiccloud_4_5.png](/images/cloud/infomaniak-public-cloud/4/infomaniak-publiccloud_4_5.png =1300x)

> Votre clé publique est importée !
> ![infomaniak-publiccloud_4_7.png](/images/cloud/infomaniak-public-cloud/4/infomaniak-publiccloud_4_7.png =1300x)
{.is-success}

# CLI
## Créer une paire de clé
```bash
openstack keypair create
    [ --private-key <FILE>]
    <NAME>
```

> **--private-key *FILE***
> - Nom du fichier de la clé privée à enregistrer. Si elle n'est pas utilisée, affiche la clé privée dans la console.
>
> **NAME**
> - Nom de la clé
>
> 	.
{.is-info}

## Importer une clé publique
```bash
openstack keypair create
    [--public-key <FILE>]
    <NAME>
```
> **--public-key *FILE***
> - Nom du fichier de la clé publique à ajouter. S'il n'est pas utilisé, crée une clé privée.
>
>
> **NAME**
> - Nom de la clé
>
> 	.
{.is-info}

## Lister les clés
```bash
openstack keypair list
```
## Supprimer une clé
```bash
openstack keypair delete <KEY>
```
> **KEY**
> - Nom de la clé à supprimer
>
> 	.
{.is-info}