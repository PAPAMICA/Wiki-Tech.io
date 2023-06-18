---
title: Utilisation des Runners de Gitlab
description: Sur cette section, nous allons aborder une approche devops. On va voir l'intégration continue et la distribution continue d'une appli web.
published: false
date: 2023-06-18T14:58:55.414Z
tags: gitlab runner, devops, ci, cd
editor: markdown
dateCreated: 2023-06-18T10:09:56.754Z
---

![gitlab](https://about.gitlab.com/images/opengraph/gitlab-blog-cover.png)

# Introduction

Avant d'aborder le projet que nous allons réaliser dans cette section, intéressons-nous à GitLab, un concurrent direct de GitHub. Ce qui le distingue de son concurrent, c'est la présence d'outils pour faciliter les déploiements, les tests unitaires, etc. Chaque projet GitLab dispose d'un onglet appelé *CI/CD*.

![onglet-cicd.png](/images/onglet-cicd.png)


1. GitLab intègre un processus de *pipelines* qui nous permet de suivre les étapes de notre fichier `.gitlab-ci.yml`, où nous pouvons spécifier les tests à effectuer avant les déploiements en production.

2. L'éditeur permet de modifier le fichier `.gitlab-ci.yml` en temps réel et de vérifier la syntaxe pour détecter d'éventuelles erreurs.

3. Un historique des pipelines est disponible pour visualiser les résultats des exécutions précédentes.

4. GitLab conserve une archive des dossiers et fichiers sauvegardés, même s'ils sont supprimés.

5. Enfin, un autre point fort est la présence de la planification des workflows, qui peut être très utile pour des tâches telles que les tests unitaires, la gestion des journaux (logs) ou les sauvegardes.



> Le fichier `.gitlab-ci.yml` est obligatoire pour établir la connexion avec les outils de GitLab. À l'intérieur de ce fichier, nous spécifions les étapes du workflow.. 
{.is-info}



>L'équivalent sur GitHub serait les actions (si vous souhaitez plus d'informations, je vous recommande de consulter la documentation pour vous donner un aperçu : [GitHub Actions](https://docs.github.com/en/actions)).

> Prochainement, un article sur Github 
{.is-info}


# Le mini projet

Parfait ! Maintenant que nous avons terminé avec l'introduction, nous pouvons passer à la partie intéressante : le mini-projet.

Avant de commencer l'explication des étapes, permettez-moi de vous donner un aperçu de l'infrastructure que nous allons utiliser pour l'intégration continue et le déploiement.

Sur le projet, on va utiliser les runners, petite des explications des **runners** :

Les runners de GitLab sont des agents d'exécution qui permettent de traiter les jobs des pipelines d'intégration continue et de déploiement. Ils sont responsables de l'exécution des différentes étapes définies dans le fichier .gitlab-ci.yml de notre projet.

Les runners peuvent être configurés pour s'exécuter sur différents environnements, tels que des machines virtuelles, des machines physiques ou même des conteneurs Docker. Ils peuvent être hébergés sur site ou dans le cloud, en fonction des besoins et des contraintes de notre infrastructure.Ils vont utilisés des **exécuteurs** qui sont charger de traiter la demande.


On peut utiliser des runners herbégé par Gitlab, ou créer un nous meme (ce qui plus intéressant).Il existe plusieurs de type d'**exécuteurs** :

  - SSH = executer des commandes à travers le SSH
  - Shell = nos scripts seront lancés dans la machine ou on retrouve le runner 
  - VirtualBox = le runner va créer une machine virtuelle à travers virtualBox
  - Docker= création de container & image docker
  - Kubernetes = création de cluster kubernetes

Pour ce mini-projet, on va utiliser la version *shell*.


---

Voici un résumé du déroulement en schèma :

> Pour se réperer, je vais nommer le runner BOB (le bricoleur)
{.is-info}

![schèma_runner.png](/images/schèma_runner.png)


Le fichier qui va permettre des connaitres les étapes à suivre pour le runner est le *gitlab-ci.yaml*


## Installation de BOB sur notre serveur


Avant de créer le runner depuis l'interface WEB. On va d'abord préparer le terrain.

On se rend sur notre terminal préferé :

```bash
sudo apt update && sudo apt upgrade
sudo curl -L --output /usr/local/bin/gitlab-runner https://gitlab-runner-downloads.s3.amazonaws.com/latest/binaries/gitlab-runner-linux-amd64
sudo chmod +x /usr/local/bin/gitlab-runner
sudo useradd --comment 'GitLab Runner' --create-home gitlab-runner --shell /bin/bash
sudo gitlab-runner install --user=gitlab-runner --working-directory=/home/gitlab-runner
sudo gitlab-runner start
```

Apres ca, nous avons le runner de gitlab qui est présent sur notre machine linux. 























