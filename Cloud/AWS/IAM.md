---
title: Identity and Access Management (IAM)
description: 
published: true
date: 2022-12-21T08:43:14.074Z
tags: aws, iam, identity
editor: markdown
dateCreated: 2022-12-20T13:55:59.092Z
---

# Identity and Access Management (IAM)

## Définition
Vous permet de contrôler l'accès aux services et aux ressources dans AWS, de gérer de manière centralisée les autorisations précises et d'analyser l'accès pour affiner les autorisations dans AWS:

- Service web qui vous permet de contrôler l'accès aux ressources AWS
- Ajouter des utilisateurs sous votre compte AWS
- Créer des utilisateurs et gérer leurs autorisations. 

> Les utilisateurs sont des personnes au sein de votre organisation et peuvent être regroupés. 
> On peut attribuer des privilèges à un groupe  
> Les groupes IAM ne peuvent contenir que des utilisateurs, pas d'autres groupes. 
> Un utilisateur peut appartenir à plusieurs groupes IAM. 
> Il n’existe pas de groupe par défaut qui inclut automatiquement tous les utilisateurs du compte AWS. L’utilisateur racine doit créer les différents groupes. 
> Le nombre et la taille des ressources IAM dans un compte AWS sont limités, il faut donc respecter le nombre de groupes maximum.
{.is-info}

## Rôle de service: 

> Les rôles sont des stratégies d’autorisations qui déterminent ce que l’identité peut ou ne peut pas faire dans AWS.

**Les rôles peuvent être utilisé par :**

- Un utilisateur IAM qui appartient au même compte AWS que le rôle
- Un utilisateur IAM dans un compte AWS différent de celui du rôle
- Un service proposé par AWS tel qu’amazon EC2
- Un utilisateur externe authentifié par un service de fournisseur d'identité externe, compatible avec SAML 2.0 ou OpenID Connect, ou un broker (intermédiaire) d'identité personnalisé. 

> voir les différents services AWS qui fonctionnent avec IAM : https://docs.aws.amazon.com/fr_fr/IAM/latest/UserGuide/reference_aws-services-that-work-with-iam.html 
{.is-info}

## Les Outils :

### IAM Credentials Report (account-level): 

**A quoi ça sert ?**

Grâce à **IAM Credentials Report**, nous pouvons générer et télécharger un rapport sur les informations d'identification qui répertorie tous les utilisateurs de notre compte et le statut de leurs informations d'identification.
- Mots de passe 
- Clés d'accès
- Dispositifs MFA 

Ce rapport peut être généré via la console de management de AWS, des kits SDK AWS et des outils de ligne de commande ou bien même de l'API IAM.

> Nous pouvons attribuer un rôle à un utilisateur externe à des fins d'audit par exemple, avec une génération à intervalle régulier d'un rapport.

### IAM Access Advisor (user-level):

Affiche les autorisations de services accordées à un utilisateur et la date du dernier accès à ces services. Pour chaque utilisateur quels sont les privilèges attribués à tel et tel utilisateurs.















