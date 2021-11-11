---
title: Infomaniak Public Cloud - Superviser sa consommation de ressources
description: Mettre en place un dashboard Grafana pour visualiser sa consommation de ressources !
published: true
date: 2021-11-05T15:21:14.335Z
tags: openstack, grafana, infomaniak, public-cloud, cloud, ipc
editor: markdown
dateCreated: 2021-11-05T15:09:16.610Z
---

![plan_de_travail_1@4x.png](/images/cloud/infomaniak-public-cloud/plan_de_travail_1@4x.png =600x)

# Introduction
Infomaniak fournit un modèle Heat fournissant une instance avec le tableau de bord Grafana ci-dessous, automatisant la récupération de toutes les métriques disponibles pour votre projet.
Les données sont récupérées deux fois par heure.
Sinon vous pouvez récupérer vous-même les métriques concernant votre projet avec le plugin gnocchi, plus de détails ici : [Billing Metering and Rating](https://docs.infomaniak.cloud/user-guide/Billing_Metering_and_Rating).

![monitoring.png](/images/cloud/infomaniak-public-cloud/8/monitoring.png =1300x)

# Installation
Vous pouvez exécuter la commande ci-dessous ou [télécharger le modèle Heat ici](https://docs.infomaniak.cloud/user-guide/monitoring/monitoring.yml).

> Ajustez les paramètres `OS_PROJECT_NAME`, `OS_USERNAME` et `OS_PASSWORD` pour correspondre à votre projet
{.is-warning}

> Vous pouvez spécifier plusieurs projets, séparez chaque identifiant par une virgule : `--parameter OS_PROJECT_NAME=PCP-PROJECT1,PCP-PROJECT2 --parameter OS_USERNAME=PCU-PROJECT1,PCU-PROJECT2 --parameter OS_PASSWORD=pass_PROJECT1,pass_PROJECT2`
{.is-info}

```bash
openstack stack create --template https://docs.infomaniak.cloud/user-guide/monitoring/monitoring.yml monitoring \
--parameter OS_PROJECT_NAME=PCP-XXXXXX \
--parameter OS_USERNAME=PCU-XXXXXX \
--parameter OS_PASSWORD=your_openstack_project_password
```

Après quelques minutes, vous pouvez accéder à votre tableau de bord Grafana en utilisant les informations taguées `output_value` à l'aide de la commande suivante :
```bash
openstack stack show monitoring -c outputs
```

Exemple : 
```bash
$ openstack stack show monitoring -c outputs

+---------+-------------------------------------------+
| Field   | Value                                     |
+---------+-------------------------------------------+
| outputs | - description: Grafana URL                |
|         |   output_key: grafana_url                 |
|         |   output_value: http://195.15.244.61:3000 |
|         | - description: Grafana login              |
|         |   output_key: grafana_login               |
|         |   output_value: admin                     |
|         | - description: Grafana password           |
|         |   output_key: grafana_password            |
|         |   output_value: CZWj5vwL7SSsoKrR          |
|         |                                           |
+---------+-------------------------------------------+
```
L'url du dashboard est `http://195.15.244.61:3000`, l'identifiant est `admin` et le mot de passe `CZWj5vwL7SSsoKrR`