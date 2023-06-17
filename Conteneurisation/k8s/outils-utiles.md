---
title: Les outils utiles pour K8S
description: 
published: true
date: 2023-06-17T11:41:29.020Z
tags: k8s
editor: markdown
dateCreated: 2023-06-16T06:33:19.574Z
---

# k9s
Your content here

# Portainer

## Prérequis

- Un cluster K3S fonctionnel
- Accès en tant qu'utilisateur avec des privilèges suffisants pour créer des objets Kubernetes dans l'espace de noms `portainer`

## Créer un espace de noms pour Portainer

1. Connectez-vous à votre cluster K3S en utilisant `kubectl`.
2. Créez un espace de noms pour Portainer en utilisant la commande suivante :

```bash
kubectl create namespace portainer
```

## Créer un secret pour la base de données

1. Créez un secret pour la base de données MySQL en utilisant la commande suivante :

```bash
kubectl create secret generic portainer-db --namespace portainer \
--from-literal="MYSQL_ROOT_PASSWORD=<password>" \
--from-literal="MYSQL_DATABASE=portainer" \
--from-literal="MYSQL_USER=portainer" \
--from-literal="MYSQL_PASSWORD=<password>"
```

Remplacez `<password>` par un mot de passe sécurisé de votre choix.

## Déployer Portainer

1. Créez un fichier `portainer.yaml` avec le contenu suivant :

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: portainer
  namespace: portainer
spec:
  selector:
    matchLabels:
      app: portainer
  replicas: 1
  template:
    metadata:
      labels:
        app: portainer
    spec:
      containers:
      - name: portainer
        image: portainer/portainer-ce:latest
        ports:
        - containerPort: 9000
        env:
        - name: DATABASE_URL
          value: mysql://portainer:<password>@portainer-db:3306/portainer?sslmode=disable
        - name: ADMIN_PASSWORD
          value: <password>
        - name: EDGE_AGENT_PORT
          value: "8000"
```

Remplacez `<password>` par un mot de passe sécurisé de votre choix.

2. Déployez Portainer en utilisant la commande suivante :

```bash
kubectl apply -f portainer.yaml
```

## Exposer Portainer en tant que service

1. Créez un fichier `portainer-service.yaml` avec le contenu suivant :

```yaml
apiVersion: v1
kind: Service
metadata:
  name: portainer
  namespace: portainer
spec:
  selector:
    app: portainer
  ports:
  - name: http
    port: 9000
    targetPort: 9000
  type: LoadBalancer
```

2. Déployez le service en utilisant la commande suivante :

```bash
kubectl apply -f portainer-service.yaml
```

## Accéder à Portainer

1. Obtenez l'adresse IP externe du service en utilisant la commande suivante :

```bash
kubectl get services -n portainer
```

2. Ouvrez un navigateur Web et accédez à l'adresse IP externe du service sur le port 9000 (par exemple, `http://<adresse_ip>:9000`).
3. Connectez-vous à Portainer en utilisant le mot de passe administrateur que vous avez défini dans le fichier `portainer.yaml`.

## Donner les droits sur le cluster
Créez un fichier `portainer-rbac.yaml` avec le contenu suivant :

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: portainer
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: default
  namespace: portainer

```
Appliquez la configuration en utilisant la commande suivante :
```bash
kubectl apply -f portainer-rbac.yaml
```

> Et voilà, vous avez maintenant installé et configuré Portainer sur votre cluster K3S !
{.is-success}
