---
title: Installation d'un POC K3S sous Proxmox
description: 
published: true
date: 2023-06-14T20:34:22.291Z
tags: k8s, proxmox
editor: markdown
dateCreated: 2023-06-14T17:52:35.877Z
---

![proxmox](https://gplexpert.com/wp-content/uploads/2019/07/PROXMOX-768x269.png =400x)

# Prérequis
## Un serveur hyperviseur
Plusieurs solutions sont disponibles, le plus simple reste le petit NUC boosté aux stéroïde. C'est vers cette solution que je me suis tourné !

Voici donc la bête que j'ai acheté spécialement pour ce POC : 

![poc_server.jpg](/Conteneurisation/poc_server.jpg)

[Disponible sur Amazon](https://amzn.to/3P9ySPp)
{.amazon}

16 Threads et 32Go de RAM seront parfait pour faire un POC K8S, voir même de la prod dans un futur proche.
J'ai cependant profité du port SATA pour ajouter un SSD de 1To au NVME de 500Go déjà disponible.

## Installer Proxmox
Je ne vous guiderai pas dans ce tutoriel dans l'installation de Proxmox, mais vous pouvez retrouver toutes la documentation necessaire directement sur internet ou peut être dans un avenir proche dans cette catégorie Wiki-Tech : https://wiki-tech.io/Virtualisation#proxmox

A la fin de l'installation de Proxmox, j'ai pour habitude d'utiliser un petit script post installation pour changer quelques configurations : 

```bash
bash -c "$(wget -qLO - https://github.com/tteck/Proxmox/raw/main/misc/post-pve-install.sh)"
```

![pve2_-_proxmox_console_2023-06-14_20.45.08@2x.png](/Conteneurisation/pve2_-_proxmox_console_2023-06-14_20.45.08@2x.png)

## Configurer le SSD de 1To
Afin de bénéficier de la place disponible dans ce SSD SATA nouvellement ajouté à la machine, il fait le configurer dans Proxmox pour permettre son utilisation.

Pour cela, rendez vous dans la catégorie `Disk` de votre hyperviseur, et cliquer sur `Create Volume Group` :
![pve2_-_proxmox_virtual_environment_2023-06-14_19.56.48@2x.png](/Conteneurisation/pve2_-_proxmox_virtual_environment_2023-06-14_19.56.48@2x.png)

> Pensez à retenir le nom de votre volume, on va en avoir besoin par la suite.
{.is-warning}


> Votre disque est prêt à être utilisé ! 
{.is-success}

## Créer un utilisateur
Par la suite je vais utiliser Packer et Terraform pour déployer l'infra rapidement, pour cela, je vais créer un utilisateur dédié :
![pve2_-_proxmox_virtual_environment_2023-06-14_20.09.41@2x.png](/Conteneurisation/pve2_-_proxmox_virtual_environment_2023-06-14_20.09.41@2x.png)
![pve2_-_proxmox_virtual_environment_2023-06-14_20.11.16@2x.png](/Conteneurisation/pve2_-_proxmox_virtual_environment_2023-06-14_20.11.16@2x.png)

Et on lui ajoute les bonnes permissions :
![pve2_-_proxmox_virtual_environment_2023-06-14_20.12.57@2x.png](/Conteneurisation/pve2_-_proxmox_virtual_environment_2023-06-14_20.12.57@2x.png)
![pve2_-_proxmox_virtual_environment_2023-06-14_20.14.46@2x.png](/Conteneurisation/pve2_-_proxmox_virtual_environment_2023-06-14_20.14.46@2x.png)

## Créer un template Debian 12
Là aussi vous êtes libre de créer ce template par les moyens que vous souhaitez, dans mon cas je vais en profiter pour utiliser Packer avec l'aide de l'excellent projet de QJoly :

[QJoly/Debian-Template-Proxmox](https://github.com/QJoly/Debian-Template-Proxmox)
{.github}

# Déployer l'infra du POC
Dans ce POC je vais avoir besoin de 3 VM.
Pour leur création, je vais utiliser Terraform.

## Préparer Terraform
Il faut installer le client Terraform sur sa machine, dans mon cas j'utilise MacOS donc je l'installe avec : 

```bash
brew install terraform
```
Ensuite nous avons besoin d'un fichier pour permettre à Terraform de se connecter à notre cluster Proxmox : `provider.tf`
```json
terraform {
  required_providers {
    proxmox = {
      source = "Telmate/proxmox"
      version = "2.9.14"
    }
  }
}

provider "proxmox" {
  pm_api_url = "https://<IP_DU_PROXMOX>:8006/api2/json"
  pm_user    = "terraform@pve"
  pm_password = "<MOT_DE_PASSE>"
  pm_tls_insecure = "true"
  pm_parallel     = 3
}
```
Et d'un autre fichier pour décrire l'infrastructure que nous voulons : `main.tf`
```json 
module "machinetest" {
  count             = 3
  source            = "QJoly/proxmox/module"
  version           = "0.0.1"
  node_name         = "master-${count.index}-tf"
  node_target       = "<NOM_DU_SERVER_PROXMOX>"
  node_qemuga       = 1
  node_pool         = ""
  node_size_disk    = "32G"
  node_bootauto     = true
  node_template     = "<NOM_DU_TEMPLATE>"
  node_storage_disk = "<NOM_DE_VOTRE_STORAGE>"
  node_network_host = "vmbr0"
  node_notes        = "Super-VM for the customer No 01"
  node_cpu          = 4
  node_memory       = 4092
}
```

Ce qui nous donnes ces deux fichiers : 
```
.
├── main.tf
└── provider.tf
```

## Déployer les serveurs
Rien de plus simple :
```bash
# On initialise Terraform
terraform init

# On vérifie ce que va faire Terraform
terraform plan

# Et on lance les joyeusetés !
terraform apply
```
Et 4 min 20 après, voici les 3 machines virtuelles qui ont bien été créées : 
![pve2_-_proxmox_virtual_environment_2023-06-14_21.28.55@2x.png](/Conteneurisation/pve2_-_proxmox_virtual_environment_2023-06-14_21.28.55@2x.png)

## Préparer les serveurs
### Changer le hostname
```bash
hostnamectl set-hostname <NOM_DU_SERVER>
```

### Configurer le terminal
Pour ça, j'utilise directement un petit script qui m'automatise toute la configuration :

https://github.com/PAPAMICA/terminal
{.github}

```bash
apt update && apt install -y curl && curl -Ls https://raw.githubusercontent.com/PAPAMICA/terminal/main/server_utils.sh | bash -s -- --motd --all-users
```

## Changer l'adresse IP
J'édite le fichier `/etc/network/interfaces` avec ces lignes :
```bash
# The primary network interface
allow-hotplug ens18
iface ens18 inet static
	address 10.10.0.250
	netmask 255.255.255.0
	gateway 10.10.0.1
	dns-nameservers 10.10.0.1 1.1.1.1
```

## Redémarrer le serveur
Et on applique toutes nos modifications avec un petit reboot :

```
reboot
```

> Il faut bien évidemment faire pareil sur les 3 serveurs en changeant les hostnames et les IP
{.is-info}


![k3s](https://cncf-branding.netlify.app/img/projects/k3s/horizontal/color/k3s-horizontal-color.png =400x)
# Installer de K3S
## Installation du Master
J'installe K3S sur le master avec commande :
```bash
curl -sfL https://get.k3s.io | sh -
```

## Récupération du Token
On peux récupérer le token de connexion au cluster sur le master :
```bash
cat /var/lib/rancher/k3s/server/node-token
```

## Installation des Nodes
On installe K3S et on rejoins le cluster avec cette commande sur les nodes : 
```bash
curl -sfL https://get.k3s.io | K3S_URL=https://<IP_DU_MASTER>:6443 K3S_TOKEN=<TOKEN_DE_CONNEXION> sh -
```
## Vérification
Deux petites commandes pour vérifier l'état du cluster à faire sur le master :
```bash
# Vérification des services essentiels :
kubectl get pods -A

# Résultat attendu :
NAMESPACE     NAME                                      READY   STATUS      RESTARTS   AGE
kube-system   local-path-provisioner-76d776f6f9-zfvl2   1/1     Running     0          16m
kube-system   coredns-59b4f5bbd5-pq9tc                  1/1     Running     0          16m
kube-system   helm-install-traefik-crd-2zcts            0/1     Completed   0          16m
kube-system   helm-install-traefik-fp9r2                0/1     Completed   1          16m
kube-system   svclb-traefik-9b862d50-55xl9              2/2     Running     0          6m15s
kube-system   traefik-57c84cf78d-xz84t                  1/1     Running     0          6m15s
kube-system   metrics-server-7b67f64457-7tvs2           1/1     Running     0          16m
kube-system   svclb-traefik-9b862d50-hmmgj              2/2     Running     0          63s
kube-system   svclb-traefik-9b862d50-w92rm              0/2     Pending     0          13s


# Vérification des noeuds :
kubectl get nodes

# Résultat attendu : 
NAME       STATUS     ROLES                  AGE     VERSION
node01     Ready      <none>                 3m23s   v1.26.5+k3s1
master01   Ready      control-plane,master   18m     v1.26.5+k3s1
node02     NotReady   <none>                 2m34s   v1.26.5+k3s1

```

> Le cluster K3S est prêt pour nos premiers tests !
{.is-success}

