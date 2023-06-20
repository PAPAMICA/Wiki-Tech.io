---
title: Boss final
description: 
published: true
date: 2023-06-20T16:11:39.976Z
tags: 
editor: markdown
dateCreated: 2023-06-20T16:05:23.769Z
---

![bossfinal-schema.png](/projets/bossfinal-schema.png)

# Gestion de projet

## Estimation du temps
1. Schématiser l'infrastructure (Draw.io) (~3h)
1. Déployer l'infra de serveur (Terraform) (~8h)
1. Préparer l'installation des serveurs (Ansible) (~6h)
1. Configurer le cluster de BDD (Galera) (~6h)
1. Déployer K8S (K8S / Kubespray) (~12h)
1. Configurer le stockage (LongHorn) (~4h)
1. Configurer les backups K8S (SwissBackup) (~3h)
1. Configurer les backups BDD (Galera / Swissbackup) (~3h)
1. Configurer la VIP (OpenStack Octavia) (~6h)
1. Mettre en place la supervision (Zabbix / Grafana / Prometheus) (~8h)
1. Déployer un premier site wordpress (~3h)

## Temps réél
1. Schématiser l'infrastructure : 3h

# Déploiement des serveurs avec Terraform
1. Création d'un projet OpenStack sur le Public Cloud d'infomaniak
