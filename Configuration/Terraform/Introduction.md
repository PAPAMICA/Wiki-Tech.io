---
title: Introduction
description: Introduction rapide à Terraform de Hashicorp
published: true
date: 2023-05-17T14:31:05.125Z
tags: provisioning, infrastructure as code, hashicorp
editor: markdown
dateCreated: 2023-05-17T14:31:05.125Z
---

![external-content.google.com.png](https://upload.wikimedia.org/wikipedia/commons/thumb/0/04/Terraform_Logo.svg/1280px-Terraform_Logo.svg.png)

# Liens pour Terraform
1. [Page Web](https://www.terraform.io/)
2. [Documentation du projet](https://developer.hashicorp.com/terraform/docs) 
3. [Terraform Registry](https://registry.terraform.io/?product_intent=terraform) (Listing de tout les providers)


> Cette page est rédigée par Lucroz94 [mon GitHub personnel](https://github.com/Lucroz94)
La rédaction est inspirée en partie de la formation suivie sur la plateforme [Eazy-Training](https://eazytraining.fr/cours/terraform-les-bases-pour-devops/) réalisée par [Dirane Willy Tafen](https://www.linkedin.com/in/dirane-willy-tafen-254913b5/) ainsi que de mes expériences en entreprise et sur mon projet de homelab.
{.is-info}

# Présentation de la page

Suite à mon suivi de la formation Terraform, j'ai compris le potentiel de cet outil et cela a changé ma vision du provisionnement d'une infrastructure complète.

Terraform se veut relativement simple de prise en main avec une syntaxe très claire, mais peut vite complexifier vos déploiements lorsque vous souhaitez variabiliser le plus possible. "Easy to learn, hard to master".

Il est important de noter que Terraform est plutôt orienté "OPS" tandis que [Pulumi](https://www.pulumi.com/) se veut plutôt orienter "DEV". D'expérience, Pulumi est très minoritaire, je vous invite donc à favoriser l'apprentissage de Terraform pour obtenir les concepts fondamentaux.

Durant les prochains articles, et ce, afin d'avoir des exemples concrets, nous déploierons des machines Linux Debian 11 afin de permettre par la suite le déploiement d'un cluster Kubernetes avec l'outil [Kubespray](https://kubespray.io/#/)

L'idée derrière cette page est de vous expliquer, de manière synthétique et avec un cas concret, quelles sont certaines possibilités que peut offrir Terraform.

Si le sujet vous intéresse, vous pouvez toujours fouiller la documentation qui est très bien rédigée, suivre une formation gratuite et ensuite en acheter une afin d'explorer plus en profondeur.

Mais tout d'abord, présentons l'outil du jour, Terraform !

# Qu'est-ce que Terraform de Hashicorp ?

Tout d'abord, qu'est-ce que nous dit Internet ?

> HashiCorp Terraform is an infrastructure as code tool that lets you define both cloud and on-prem resources in human-readable configuration files that you can version, reuse, and share. You can then use a consistent workflow to provision and manage all of your infrastructure throughout its lifecycle. Terraform can manage low-level components like compute, storage, and networking resources, as well as high-level components like DNS entries and SaaS features.
>

**Une brève traduction en Français :**

> HashiCorp Terraform est un outil d'infrastructure as code qui vous permet de définir à la fois des ressources Cloud et OnPremise dans des fichiers de configuration lisibles, que vous pouvez versionner, réutiliser et partager. Vous pouvez ensuite utiliser un flux de travail cohérent pour provisionner et gérer l'ensemble de votre infrastructure tout au long de son cycle de vie. Terraform peut gérer des composants de bas niveau tels que le calcul, le stockage et les ressources réseau, ainsi que des composants de haut niveau tels que les entrées DNS et les fonctionnalités SaaS.

Désormais, avec de simples fichiers en HCL (si vous utilisez d'autres outils d'Hashicorp, vous ne serez pas perdu.), il est possible de déployer, modifier à chaud certains éléments et détruire un environnement complet de machines en un claquement de doigts. Le gain de temps et de granularité que permet cet outil devient une composante essentielle pour le déploiement d'infrastructure. De plus, il se marie parfaitement avec des pipelines de CI/CD permettant d'automatiser encore plus le déploiement et, par exemple, faciliter la QA de vos applications.

La très grande force de Terraform sont ses providers (À l'écriture de l'article : 3 182 providers, 13 433 modules), lui permettant d'être agnostique quant à l'infrastructure cible, dont je détaillerai l'importance plus tard.

## Conclusion : 
Pour faire une petite comparaison avec Docker, Terraform est le comportement d'un Docker-compose, mais pour l'entièreté de vos machines virtuelles ! Vous pouvez ainsi, via la création d'un ou plusieurs fichiers Terraform, déclarer tout votre environnement virtuelles des couches les plus basses (combien de CPU/RAM, network, disques à partager...) jusqu'aux couches plus hautes. (VPC/déclarations de policies IAM AWS...)

Maintenant, allons un peu plus en détail concernant la force principale de Terraform : les providers.