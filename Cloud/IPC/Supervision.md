---
title: Infomaniak Public Cloud - Superviser sa consommation de ressources
description: Mettre en place un dashboard Grafana pour visualiser sa consommation de ressources !
published: true
date: 2021-11-05T15:09:16.610Z
tags: openstack, grafana, infomaniak, public-cloud, cloud, ipc
editor: markdown
dateCreated: 2021-11-05T15:09:16.610Z
---

![plan_de_travail_1@4x.png](/images/cloud/infomaniak-public-cloud/plan_de_travail_1@4x.png =600x)

# Introduction
Infomaniak fournit un modèle Heat fournissant une instance avec le tableau de bord Grafana ci-dessous, automatisant la récupération de toutes les métriques disponibles pour votre projet.
Les données sont récupérées deux fois par heure.
Sinon vous pouvez récupérer vous-même les métriques concernant votre projet avec le plugin gnocchi, plus de détails ici : [Billing Metering and Rating](https://docs.infomaniak.cloud/user-guide/Billing_Metering_and_Rating).