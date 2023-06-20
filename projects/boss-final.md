---
title: Boss final
description: 
published: true
date: 2023-06-20T19:28:14.530Z
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
2. Déployer l'infrastrucure avec Terraform : 3h

# Déploiement des serveurs avec Terraform
1. Création d'un projet OpenStack sur le Public Cloud d'infomaniak
2. Récupération des identifiants
3. Connecter Terraform à OpenStack
```json
# Define required providers
terraform {
  required_providers {
    openstack = {
      source  = "terraform-provider-openstack/openstack"
      version = "1.44.0"
    }
  }
}

# Configure the OpenStack Provider
provider "openstack" {
  auth_url = "https://api.pub1.infomaniak.cloud/identity"
  region = "dc3-a"
  user_name = "<USERNAME>"
  password = "<PASSWORD>"
  user_domain_name = "Default"
  project_domain_id = "default"
  tenant_id = "<PROJECT_ID>"
  tenant_name = "<PROJECT_NAME>"
}
```
4. Ajouter sa Yubikey
```json
# Upload public key
resource "openstack_compute_keypair_v2" "yubikey" {
  name = "yubikey"
  public_key = "<PUBLIC_KEY>"
}
```
5. Création des security group
```json
# Define the security group
resource "openstack_compute_secgroup_v2" "ICMP" {
  name        = "ICMP"
  description = "Allow Ping"

  rule {
    from_port   = -1
    to_port     = -1
    ip_protocol = "icmp"
    cidr        = "0.0.0.0/0"
  }
}

# Create security group
resource "openstack_compute_secgroup_v2" "ALL-LOCAL" {
  name        = "ALL-LOCAL"
  description = "All access on 10.99.0.0/24"

  # Rule for all traffic
  rule {
    from_port   = 1
    to_port     = 65535
    ip_protocol = "tcp"
    cidr        = "10.99.0.0/24"
  }

}


resource "openstack_compute_secgroup_v2" "SSH-EXTERNE" {
  name        = "SSH-EXTERNE"
  description = "SSH from anywhere"

  rule {
    from_port   = 22
    to_port     = 22
    ip_protocol = "tcp"
    cidr        = "0.0.0.0/0"
  }
}

resource "openstack_compute_secgroup_v2" "HTTP-HTTPS" {
  name        = "HTTP-HTTPS"
  description = "Web"

  rule {
    from_port   = 80
    to_port     = 80
    ip_protocol = "tcp"
    cidr        = "0.0.0.0/0"
  }

  rule {
    from_port   = 443
    to_port     = 443
    ip_protocol = "tcp"
    cidr        = "0.0.0.0/0"
  }
}  
```
6. Création du réseau privé
```json
resource "openstack_networking_network_v2" "private_network" {
  name = "private_network"
  admin_state_up = true
}

resource "openstack_networking_subnet_v2" "private_subnet" {
  name = "private_subnet"
  network_id = openstack_networking_network_v2.private_network.id
  cidr = "10.99.0.0/24"
  ip_version = 4
  dns_nameservers = ["1.1.1.1"]
}
```
7. Création du controller
```json
resource "openstack_compute_instance_v2" "controller" {
  name            = "controller"
  image_id        = "a220f306-1488-4788-9dcc-b94ed1338662"
  flavor_name     = "a1-ram2-disk80-perf1"
  key_pair        = "yubikey"
  security_groups = ["ALL-LOCAL", "SSH-EXTERNE", "HTTP-HTTPS", "ICMP"]

  metadata = {
    type = "controller"
  }
  network {
    name = "ext-net1"
  }
  network {
    name = openstack_networking_network_v2.private_network.name
    fixed_ip_v4 = "10.99.0.10"
  }
}
```
8. Création des masters
```json
resource "openstack_compute_instance_v2" "master" {
  count           = 3
  name            = "master-0${count.index + 1}"
  flavor_name     = "a1-ram2-disk80-perf1"
  image_id        = "a220f306-1488-4788-9dcc-b94ed1338662"
  key_pair        = "yubikey"
  security_groups = ["ALL-LOCAL", "HTTP-HTTPS"]
  availability_zone = element(["dc3-a-04", "dc3-a-09", "dc3-a-10"], count.index)

  metadata = {
    type = "master"
  }

  network {
    name = openstack_networking_network_v2.private_network.name
    fixed_ip_v4 = "10.99.0.1${count.index + 1}"
  }
}
```
9. Création des workers
```json
resource "openstack_compute_instance_v2" "worker" {
  count           = 3
  name            = "worker-0${count.index + 1}"
  flavor_name     = "a1-ram2-disk80-perf1"
  image_id        = "a220f306-1488-4788-9dcc-b94ed1338662"
  key_pair        = "yubikey"
  security_groups = ["ALL-LOCAL", "HTTP-HTTPS"]
  availability_zone = element(["dc3-a-04", "dc3-a-09", "dc3-a-10"], count.index)

  metadata = {
    type = "worker"
  }

  network {
    name = openstack_networking_network_v2.private_network.name
    fixed_ip_v4 = "10.99.0.10${count.index + 1}"
  }
}
```

<details>
<summary>Voir le fichier complet</summary>
  
```json
# Define required providers
terraform {
  required_providers {
    openstack = {
      source  = "terraform-provider-openstack/openstack"
      version = "1.44.0"
    }
  }
}

# Configure the OpenStack Provider
provider "openstack" {
  auth_url = "https://api.pub1.infomaniak.cloud/identity"
  region = "dc3-a"
  user_name = "<USERNAME>"
  password = "<PASSWORD>"
  user_domain_name = "Default"
  project_domain_id = "default"
  tenant_id = "<PROJECT_ID>"
  tenant_name = "<PROJECT_NAME>"
}

# Upload public key
resource "openstack_compute_keypair_v2" "yubikey" {
  name = "yubikey"
  public_key = "<PUBLIC_KEY>"
}

# Define the security group
resource "openstack_compute_secgroup_v2" "ICMP" {
  name        = "ICMP"
  description = "Allow Ping"

  rule {
    from_port   = -1
    to_port     = -1
    ip_protocol = "icmp"
    cidr        = "0.0.0.0/0"
  }
}

# Create security group
resource "openstack_compute_secgroup_v2" "ALL-LOCAL" {
  name        = "ALL-LOCAL"
  description = "All access on 10.99.0.0/24"

  # Rule for all traffic
  rule {
    from_port   = 1
    to_port     = 65535
    ip_protocol = "tcp"
    cidr        = "10.99.0.0/24"
  }

}


resource "openstack_compute_secgroup_v2" "SSH-EXTERNE" {
  name        = "SSH-EXTERNE"
  description = "SSH from anywhere"

  rule {
    from_port   = 22
    to_port     = 22
    ip_protocol = "tcp"
    cidr        = "0.0.0.0/0"
  }
}

resource "openstack_compute_secgroup_v2" "HTTP-HTTPS" {
  name        = "HTTP-HTTPS"
  description = "Web"

  rule {
    from_port   = 80
    to_port     = 80
    ip_protocol = "tcp"
    cidr        = "0.0.0.0/0"
  }

  rule {
    from_port   = 443
    to_port     = 443
    ip_protocol = "tcp"
    cidr        = "0.0.0.0/0"
  }
}  

resource "openstack_networking_network_v2" "private_network" {
  name = "private_network"
  admin_state_up = true
}

resource "openstack_networking_subnet_v2" "private_subnet" {
  name = "private_subnet"
  network_id = openstack_networking_network_v2.private_network.id
  cidr = "10.99.0.0/24"
  ip_version = 4
  dns_nameservers = ["1.1.1.1"]
}

# Create a controller
resource "openstack_compute_instance_v2" "controller" {
  name            = "controller"
  image_id        = "a220f306-1488-4788-9dcc-b94ed1338662"
  flavor_name     = "a1-ram2-disk80-perf1"
  key_pair        = "yubikey"
  security_groups = ["ALL-LOCAL", "SSH-EXTERNE", "HTTP-HTTPS", "ICMP"]

  metadata = {
    application = "web-app"
  }
  network {
    name = "ext-net1"
  }
  network {
    name = openstack_networking_network_v2.private_network.name
    fixed_ip_v4 = "10.99.0.10"
  }
}

# Define the instance
resource "openstack_compute_instance_v2" "master" {
  count           = 3
  name            = "master-0${count.index + 1}"
  flavor_name     = "a1-ram2-disk80-perf1"
  image_id        = "a220f306-1488-4788-9dcc-b94ed1338662"
  key_pair        = "yubikey"
  security_groups = ["ALL-LOCAL", "HTTP-HTTPS"]
  availability_zone = element(["dc3-a-04", "dc3-a-09", "dc3-a-10"], count.index)

  network {
    name = openstack_networking_network_v2.private_network.name
    fixed_ip_v4 = "10.99.0.1${count.index + 1}"
  }
}

# Define the instance
resource "openstack_compute_instance_v2" "worker" {
  count           = 3
  name            = "worker-0${count.index + 1}"
  flavor_name     = "a1-ram2-disk80-perf1"
  image_id        = "a220f306-1488-4788-9dcc-b94ed1338662"
  key_pair        = "yubikey"
  security_groups = ["ALL-LOCAL", "HTTP-HTTPS"]
  availability_zone = element(["dc3-a-04", "dc3-a-09", "dc3-a-10"], count.index)

  network {
    name = openstack_networking_network_v2.private_network.name
    fixed_ip_v4 = "10.99.0.10${count.index + 1}"
  }
}
```
</details>

