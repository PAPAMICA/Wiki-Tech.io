---
title: Kubernetes - Cheat Sheet
description: Un petit pense bête pour les principales commandes de K8S
published: true
date: 2023-06-16T11:26:39.666Z
tags: cheatsheet, k8s
editor: markdown
dateCreated: 2023-06-16T07:00:45.096Z
---

![logo](https://upload.wikimedia.org/wikipedia/commons/thumb/6/67/Kubernetes_logo.svg/2560px-Kubernetes_logo.svg.png =400x)

# Autocompletion
## Bash


### tables {.tabset}
#### <svg xmlns="http://www.w3.org/2000/svg" height="1em" viewBox="0 0 384 512"><!--! Font Awesome Free 6.4.0 by @fontawesome - https://fontawesome.com License - https://fontawesome.com/license (Commercial License) Copyright 2023 Fonticons, Inc. --><path d="M318.7 268.7c-.2-36.7 16.4-64.4 50-84.8-18.8-26.9-47.2-41.7-84.7-44.6-35.5-2.8-74.3 20.7-88.5 20.7-15 0-49.4-19.7-76.4-19.7C63.3 141.2 4 184.8 4 273.5q0 39.3 14.4 81.2c12.8 36.7 59 126.7 107.2 125.2 25.2-.6 43-17.9 75.8-17.9 31.8 0 48.3 17.9 76.4 17.9 48.6-.7 90.4-82.5 102.6-119.3-65.2-30.7-61.7-90-61.7-91.9zm-56.6-164.2c27.3-32.4 24.8-61.9 24-72.5-24.1 1.4-52 16.4-67.9 34.9-17.5 19.8-27.8 44.3-25.6 71.9 26.1 2 49.9-11.4 69.5-34.3z"/></svg> Macos
Installer `bash-completion`
```bash
brew install bash-completion

echo "[ -f /usr/local/etc/bash_completion ] && . /usr/local/etc/bash_completion" >> ~/.bash_profile
```
Ajouter la completion pour kubectl
```bash
echo "source <(kubectl completion bash)" >> ~/.bashrc
```
Sourcer le fichier
```bash
source ~/.bashrc
```

#### <svg xmlns="http://www.w3.org/2000/svg" height="1em" viewBox="0 0 448 512"><!--! Font Awesome Free 6.4.0 by @fontawesome - https://fontawesome.com License - https://fontawesome.com/license (Commercial License) Copyright 2023 Fonticons, Inc. --><path d="M220.8 123.3c1 .5 1.8 1.7 3 1.7 1.1 0 2.8-.4 2.9-1.5.2-1.4-1.9-2.3-3.2-2.9-1.7-.7-3.9-1-5.5-.1-.4.2-.8.7-.6 1.1.3 1.3 2.3 1.1 3.4 1.7zm-21.9 1.7c1.2 0 2-1.2 3-1.7 1.1-.6 3.1-.4 3.5-1.6.2-.4-.2-.9-.6-1.1-1.6-.9-3.8-.6-5.5.1-1.3.6-3.4 1.5-3.2 2.9.1 1 1.8 1.5 2.8 1.4zM420 403.8c-3.6-4-5.3-11.6-7.2-19.7-1.8-8.1-3.9-16.8-10.5-22.4-1.3-1.1-2.6-2.1-4-2.9-1.3-.8-2.7-1.5-4.1-2 9.2-27.3 5.6-54.5-3.7-79.1-11.4-30.1-31.3-56.4-46.5-74.4-17.1-21.5-33.7-41.9-33.4-72C311.1 85.4 315.7.1 234.8 0 132.4-.2 158 103.4 156.9 135.2c-1.7 23.4-6.4 41.8-22.5 64.7-18.9 22.5-45.5 58.8-58.1 96.7-6 17.9-8.8 36.1-6.2 53.3-6.5 5.8-11.4 14.7-16.6 20.2-4.2 4.3-10.3 5.9-17 8.3s-14 6-18.5 14.5c-2.1 3.9-2.8 8.1-2.8 12.4 0 3.9.6 7.9 1.2 11.8 1.2 8.1 2.5 15.7.8 20.8-5.2 14.4-5.9 24.4-2.2 31.7 3.8 7.3 11.4 10.5 20.1 12.3 17.3 3.6 40.8 2.7 59.3 12.5 19.8 10.4 39.9 14.1 55.9 10.4 11.6-2.6 21.1-9.6 25.9-20.2 12.5-.1 26.3-5.4 48.3-6.6 14.9-1.2 33.6 5.3 55.1 4.1.6 2.3 1.4 4.6 2.5 6.7v.1c8.3 16.7 23.8 24.3 40.3 23 16.6-1.3 34.1-11 48.3-27.9 13.6-16.4 36-23.2 50.9-32.2 7.4-4.5 13.4-10.1 13.9-18.3.4-8.2-4.4-17.3-15.5-29.7zM223.7 87.3c9.8-22.2 34.2-21.8 44-.4 6.5 14.2 3.6 30.9-4.3 40.4-1.6-.8-5.9-2.6-12.6-4.9 1.1-1.2 3.1-2.7 3.9-4.6 4.8-11.8-.2-27-9.1-27.3-7.3-.5-13.9 10.8-11.8 23-4.1-2-9.4-3.5-13-4.4-1-6.9-.3-14.6 2.9-21.8zM183 75.8c10.1 0 20.8 14.2 19.1 33.5-3.5 1-7.1 2.5-10.2 4.6 1.2-8.9-3.3-20.1-9.6-19.6-8.4.7-9.8 21.2-1.8 28.1 1 .8 1.9-.2-5.9 5.5-15.6-14.6-10.5-52.1 8.4-52.1zm-13.6 60.7c6.2-4.6 13.6-10 14.1-10.5 4.7-4.4 13.5-14.2 27.9-14.2 7.1 0 15.6 2.3 25.9 8.9 6.3 4.1 11.3 4.4 22.6 9.3 8.4 3.5 13.7 9.7 10.5 18.2-2.6 7.1-11 14.4-22.7 18.1-11.1 3.6-19.8 16-38.2 14.9-3.9-.2-7-1-9.6-2.1-8-3.5-12.2-10.4-20-15-8.6-4.8-13.2-10.4-14.7-15.3-1.4-4.9 0-9 4.2-12.3zm3.3 334c-2.7 35.1-43.9 34.4-75.3 18-29.9-15.8-68.6-6.5-76.5-21.9-2.4-4.7-2.4-12.7 2.6-26.4v-.2c2.4-7.6.6-16-.6-23.9-1.2-7.8-1.8-15 .9-20 3.5-6.7 8.5-9.1 14.8-11.3 10.3-3.7 11.8-3.4 19.6-9.9 5.5-5.7 9.5-12.9 14.3-18 5.1-5.5 10-8.1 17.7-6.9 8.1 1.2 15.1 6.8 21.9 16l19.6 35.6c9.5 19.9 43.1 48.4 41 68.9zm-1.4-25.9c-4.1-6.6-9.6-13.6-14.4-19.6 7.1 0 14.2-2.2 16.7-8.9 2.3-6.2 0-14.9-7.4-24.9-13.5-18.2-38.3-32.5-38.3-32.5-13.5-8.4-21.1-18.7-24.6-29.9s-3-23.3-.3-35.2c5.2-22.9 18.6-45.2 27.2-59.2 2.3-1.7.8 3.2-8.7 20.8-8.5 16.1-24.4 53.3-2.6 82.4.6-20.7 5.5-41.8 13.8-61.5 12-27.4 37.3-74.9 39.3-112.7 1.1.8 4.6 3.2 6.2 4.1 4.6 2.7 8.1 6.7 12.6 10.3 12.4 10 28.5 9.2 42.4 1.2 6.2-3.5 11.2-7.5 15.9-9 9.9-3.1 17.8-8.6 22.3-15 7.7 30.4 25.7 74.3 37.2 95.7 6.1 11.4 18.3 35.5 23.6 64.6 3.3-.1 7 .4 10.9 1.4 13.8-35.7-11.7-74.2-23.3-84.9-4.7-4.6-4.9-6.6-2.6-6.5 12.6 11.2 29.2 33.7 35.2 59 2.8 11.6 3.3 23.7.4 35.7 16.4 6.8 35.9 17.9 30.7 34.8-2.2-.1-3.2 0-4.2 0 3.2-10.1-3.9-17.6-22.8-26.1-19.6-8.6-36-8.6-38.3 12.5-12.1 4.2-18.3 14.7-21.4 27.3-2.8 11.2-3.6 24.7-4.4 39.9-.5 7.7-3.6 18-6.8 29-32.1 22.9-76.7 32.9-114.3 7.2zm257.4-11.5c-.9 16.8-41.2 19.9-63.2 46.5-13.2 15.7-29.4 24.4-43.6 25.5s-26.5-4.8-33.7-19.3c-4.7-11.1-2.4-23.1 1.1-36.3 3.7-14.2 9.2-28.8 9.9-40.6.8-15.2 1.7-28.5 4.2-38.7 2.6-10.3 6.6-17.2 13.7-21.1.3-.2.7-.3 1-.5.8 13.2 7.3 26.6 18.8 29.5 12.6 3.3 30.7-7.5 38.4-16.3 9-.3 15.7-.9 22.6 5.1 9.9 8.5 7.1 30.3 17.1 41.6 10.6 11.6 14 19.5 13.7 24.6zM173.3 148.7c2 1.9 4.7 4.5 8 7.1 6.6 5.2 15.8 10.6 27.3 10.6 11.6 0 22.5-5.9 31.8-10.8 4.9-2.6 10.9-7 14.8-10.4s5.9-6.3 3.1-6.6-2.6 2.6-6 5.1c-4.4 3.2-9.7 7.4-13.9 9.8-7.4 4.2-19.5 10.2-29.9 10.2s-18.7-4.8-24.9-9.7c-3.1-2.5-5.7-5-7.7-6.9-1.5-1.4-1.9-4.6-4.3-4.9-1.4-.1-1.8 3.7 1.7 6.5z"/></svg> Linux
Installer `bash-completion`
```bash
sudo apt-get install -y bash-completion
```
Ajouter la completion pour kubectl
```bash
echo "source <(kubectl completion bash)" >> ~/.bashrc
```
Sourcer le fichier
```bash
source ~/.bashrc
```

## Zsh
Ajouter la completion pour kubectl 
```bash
echo '[[ $commands[kubectl] ]] && source <(kubectl completion zsh)' >> ~/.zshrc
```

## FISH
Ajouter la completion pour kubectl 
```bash
kubectl completion fish > ~/.config/fish/completions/kubectl.fish
```

## Powershell
Ajouter la completion pour kubectl 

```powershell
kubectl completion powershell > $HOME\.kube\completion.ps1
Add-Content $PROFILE "$HOME\.kube\completion.ps1"
```


# kubectl
## Gestion de cluster

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

## Daemonsets

> Shortcode = ds
{.is-info}


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

## Déploiements

> Shortcode = deploy
{.is-info}


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

## Événements

> Shortcode = ev
{.is-info}


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

## Journaux

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

> Pour les journaux, nous recommandons également l'utilisation d'un outil développé par Johan Haleby appelé Kubetail. Il s'agit d'un script bash qui vous permettra d'obtenir des journaux à partir de plusieurs pods simultanément. Vous pouvez en savoir plus sur son dépôt Github. Voir [kubetail](#kubetail)
{.is-info}

## Fichiers de manifeste

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

## Namespace

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

## Noeuds

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

## Pods

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

## Contrôleurs de réplication

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

## ReplicaSets

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

## Secrets

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

## Services

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

## Service Accounts
> Shortcode = sa
{.is-info}


Liste des comptes de service
```
kubectl get serviceaccounts
```

Afficher l'état détaillé d'un ou plusieurs comptes de service
```
kubectl describe serviceaccounts
```

Remplacer un compte de service
```
kubectl replace serviceaccount
```

Supprimer un compte de service
```
kubectl delete serviceaccount <service_account_name>
```

## StatefulSet
> Shortcode = sts
{.is-info}


Liste des StatefulSet
```
kubectl get statefulset
```

Supprimer uniquement le StatefulSet (pas les pods)
```
kubectl delete statefulset/[stateful_set_name] --cascade=false
```

## Options courantes
Dans Kubectl, vous pouvez spécifier des indicateurs facultatifs avec les commandes. Voici quelques-uns des plus courants et utiles.

- **-o Format de sortie.** Par exemple, si vous souhaitez répertorier tous les pods dans le format de sortie ps avec plus d'informations.
```
kubectl get pods -o wide
```

- **-n Raccourci pour --namespace.** Par exemple, si vous souhaitez répertorier tous les pods dans un Namespace spécifique, vous exécuteriez cette commande :
```
kubectl get pods --namespace=[namespace_name]
```
ou
```
kubectl get pods -n=[namespace_name]
```

- **-f Nom de fichier, répertoire ou URL vers les fichiers à utiliser pour créer une ressource.** Par exemple, lors de la création d'un pod en utilisant des données dans un fichier nommé newpod.json.
```
kubectl create -f ./newpod.json
```

- **-l Sélecteur de filtre, prend en charge '=', '==' et '!='.**

## Aide pour kubectl
```
-h
```

# kubetail
Obtenir les journaux pour tous les pods nommés avec le préfixe pod

```
kubetail <pod_prefix>
```

Inclure les 5 dernières minutes de journaux

```
kubetail <pod_prefix> -s 5m
```

# kubens et kubectx
  
Pour simplifier le changement de context/namespace par défault *(pouvant se faire nativement par la commande `kubectl config set-context –current –namespace=toto`)*, il est possible d'utiliser les utilitaires **kubens** et **kubectx** qui permettre de rapidement modifier la configuration de votre *KUBECONFIG*.
  
## Installation 

### MacOS
  
```bash
brew install kubectx
```
### Linux (manuel)
  
```bash
sudo git clone https://github.com/ahmetb/kubectx /opt/kubectx
sudo ln -s /opt/kubectx/kubectx /usr/local/bin/kubectx
sudo ln -s /opt/kubectx/kubens /usr/local/bin/kubens
```
  
## Usage

### Changer de contexte (kubectx)

```bash
kubectx prod # Utiliser le contexte prod
kubectx - # Revenir sur le précédent contexte
```
  
### Changer de namespace par défaut

```bash
kubens kube-system # basculer sur le namespace kube-system
kubens - # Revenir au précédent namespace
```
## Autre
Il est possible d'utiliser kubens et kubectx en interactif avec **fzf** disponible sur votre path.
   - Exemple:
![Kubectx en interactif avec fzf](https://github.com/ahmetb/kubectx/raw/master/img/kubectx-interactive.gif)

### 
# k9s
A venir 😜