---
title: Kubernetes - Cheat Sheet
description: Un petit pense bête pour les principales commandes de K8S
published: true
date: 2023-06-16T07:00:45.096Z
tags: cheatsheet, k8s
editor: markdown
dateCreated: 2023-06-16T07:00:45.096Z
---

# Gestion de cluster

Afficher les informations d'endpoint sur le maître et les services dans le cluster

```
kubectl cluster-info
```

Afficher la version de Kubernetes en cours d'exécution sur le client et le serveur

```
kubectl version
```

Obtenir la configuration du cluster

```
kubectl config view
```

Lister les ressources API disponibles

```
kubectl api-resources
```

Lister les versions API disponibles

```
kubectl api-versions
```

Lister tout

```
kubectl get all --all-namespaces
```

# Daemonsets

Shortcode = ds

Lister un ou plusieurs daemonsets

```
kubectl get daemonset
```

Modifier et mettre à jour la définition d'un ou plusieurs daemonsets

```
kubectl edit daemonset <daemonset_name>
```

Supprimer un daemonset

```
kubectl delete daemonset <daemonset_name>
```

Créer un nouveau daemonset

```
kubectl create daemonset <daemonset_name>
```

Gérer le déploiement d'un daemonset

```
kubectl rollout daemonset
```

Afficher l'état détaillé des daemonsets dans un espace de noms

```
kubectl describe ds <daemonset_name> -n <namespace_name>
```

# Déploiements

Shortcode = deploy

Lister un ou plusieurs déploiements

```
kubectl get deployment
```

Afficher l'état détaillé d'un ou plusieurs déploiements

```
kubectl describe deployment <deployment_name>
```

Modifier et mettre à jour la définition d'un ou plusieurs déploiements sur le serveur

```
kubectl edit deployment <deployment_name>
```

Créer un nouveau déploiement

```
kubectl create deployment <deployment_name>
```

Supprimer des déploiements

```
kubectl delete deployment <deployment_name>
```

Voir l'état de déploiement d'un déploiement

```
kubectl rollout status deployment <deployment_name>
```

# Événements

Shortcode = ev

Lister les événements récents pour toutes les ressources du système

```
kubectl get events
```

Lister uniquement les avertissements

```
kubectl get events --field-selector type=Warning
```

Lister les événements mais exclure les événements de Pod

```
kubectl get events --field-selector involvedObject.kind!=Pod
```

Extraire les événements pour un seul nœud avec un nom spécifique

```
kubectl get events --field-selector involvedObject.kind=Node, involvedObject.name=<node_name>
```

Filtrer les événements normaux d'une liste d'événements

```
kubectl get events --field-selector type!=Normal
```

# Journaux

Afficher les journaux pour un pod

```
kubectl logs <pod_name>
```

Afficher les journaux pour la dernière heure pour un pod

```
kubectl logs --since=1h <pod_name>
```

Obtenir les 20 dernières lignes de journaux

```
kubectl logs --tail=20 <pod_name>
```

Obtenir les journaux d'un service et sélectionner éventuellement le conteneur

```
kubectl logs -f <service_name> [-c <$container>]
```

Afficher les journaux pour un pod et suivre les nouveaux journaux

```
kubectl logs -f <pod_name>
```

Afficher les journaux pour un conteneur dans un pod

```
kubectl logs -c <container_name> <pod_name>
```

Exporter les journaux d'un pod dans un fichier nommé 'pod.log'

```
kubectl logs <pod_name> pod.log
```

Afficher les journaux pour un pod précédemment en panne

```
kubectl logs --previous <pod_name>
```

> Pour les journaux, nous recommandons également l'utilisation d'un outil développé par Johan Haleby appelé Kubetail. Il s'agit d'un script bash qui vous permettra d'obtenir des journaux à partir de plusieurs pods simultanément. Vous pouvez en savoir plus à son dépôt Github. Voici quelques commandes d'exemple utilisant Kubetail.
{.is-info}


Obtenir les journaux pour tous les pods nommés avec le préfixe pod

```
kubetail <pod_prefix>
```

Inclure les 5 dernières minutes de journaux

```
kubetail <pod_prefix> -s 5m
```

# Fichiers de manifeste

> Une autre option pour modifier les objets est d'utiliser des fichiers de manifeste. Nous recommandons vivement d'utiliser cette méthode. Elle consiste à utiliser des fichiers YAML avec toutes les options nécessaires pour configurer les objets. Nous avons nos fichiers YAML stockés dans un référentiel Git, afin de pouvoir suivre les modifications et rationaliser les modifications.
{.is-info}


Appliquer une configuration à un objet par nom de fichier ou stdin. Remplace la configuration existante.

```
kubectl apply -f manifest_file.yaml
```

Créer des objets

```
kubectl create -f manifest_file.yaml
```

Créer des objets dans tous les fichiers de manifeste d'un répertoire

```
kubectl create -f ./dir
```

Créer des objets à partir d'une URL

```
kubectl create -f 'url'
```

Supprimer un objet

```
kubectl delete -f manifest_file.yaml
```

# Namespace

> Shortcode = ns
{.is-info}


Créer un espace de noms <name>

```
kubectl create namespace <namespace_name>
```

Lister un ou plusieurs espaces de noms

```
kubectl get namespace <namespace_name>
```

Afficher l'état détaillé d'un ou plusieurs espaces de noms

```
kubectl describe namespace <namespace_name>
```

Supprimer un espace de noms

```
kubectl delete namespace <namespace_name>
```

Modifier et mettre à jour la définition d'un espace de noms

```
kubectl edit namespace <namespace_name>
```

Afficher l'utilisation des ressources (CPU/mémoire/stockage) pour un espace de noms

```
kubectl top namespace <namespace_name>
```

# Noeuds

> Shortcode = no
{.is-info}


Mettre à jour les taints sur un ou plusieurs nœuds

```
kubectl taint node <node_name>
```

Lister un ou plusieurs nœuds

```
kubectl get node
```

Supprimer un ou plusieurs nœuds

```
kubectl delete node <node_name>
```

Afficher l'utilisation des ressources (CPU/mémoire/stockage) pour les nœuds

```
kubectl top node
```

Allocation de ressources par nœud

```
kubectl describe nodes | grep Allocated -A 5
```

Pods en cours d'exécution sur un nœud

```
kubectl get pods -o wide | grep <node_name>
```

Annoter un nœud

```
kubectl annotate node <node_name>
```

Marquer un nœud comme non programmable

```
kubectl cordon node <node_name>
```

Marquer un nœud comme programmable

```
kubectl uncordon node <node_name>
```

Drainer un nœud en préparation à la maintenance

```
kubectl drain node <node_name>
```

Ajouter ou mettre à jour les étiquettes d'un ou plusieurs nœuds

```
kubectl label node
```

# Pods

> Shortcode = po
{.is-info}


Lister un ou plusieurs pods

```
kubectl get pod
```

Supprimer un pod

```
kubectl delete pod <pod_name>
```

Afficher l'état détaillé d'un pod

```
kubectl describe pod <pod_name>
```

Créer un pod

```
kubectl create pod <pod_name>
```

Exécuter une commande contre un conteneur dans un pod

```
kubectl exec <pod_name> -c <container_name> <command>
```

Obtenir un shell interactif sur un pod à conteneur unique

```
kubectl exec -it <pod_name> /bin/sh
```

Afficher l'utilisation des ressources (CPU/mémoire/stockage) pour les pods

```
kubectl top pod
```

Ajouter ou mettre à jour les annotations d'un pod

```
kubectl annotate pod <pod_name> <annotation>
```

Ajouter ou mettre à jour l'étiquette d'un pod

```
kubectl label pod <pod_name>
```

# Contrôleurs de réplication

> Shortcode = rc
{.is-info}


Lister les contrôleurs de réplication

```
kubectl get rc
```

Lister les contrôleurs de réplication par espace de noms

```
kubectl get rc --namespace="<namespace_name>"
```

# ReplicaSets

> Shortcode = rs
{.is-info}


Lister les ReplicaSets

```
kubectl get replicasets
```

Afficher l'état détaillé d'un ou plusieurs ReplicaSets

```
kubectl describe replicasets <replicaset_name>
```

Mettre à l'échelle un ReplicaSet

```
kubectl scale --replicas=[x]
```

# Secrets

Créer un secret

```
kubectl create secret
```

Lister les secrets

```
kubectl get secrets
```

Lister les détails sur les secrets

```
kubectl describe secrets
```

Supprimer un secret

```
kubectl delete secret <secret_name>
```

# Services

> Shortcode = svc
{.is-info}


Lister un ou plusieurs services

```
kubectl get services
```

Afficher l'état détaillé d'un service

```
kubectl describe services
```

Exposer un contrôleur de réplication, un service, un déploiement ou un pod en tant que nouveau service Kubernetes

```
kubectl expose deployment [deployment_name]
```

Modifier et mettre à jour la définition d'un ou plusieurs services

```
kubectl edit services
```


