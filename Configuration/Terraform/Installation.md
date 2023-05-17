---
title: Installation
description: Comment installer Terraform sur son environnement
published: true
date: 2023-05-17T15:47:34.634Z
tags: infrastructure as code, hashicorp, iac
editor: markdown
dateCreated: 2023-05-17T15:47:34.634Z
---

![external-content.google.com.png](https://upload.wikimedia.org/wikipedia/commons/thumb/0/04/Terraform_Logo.svg/1280px-Terraform_Logo.svg.png)
# Installation de Terraform et initialisation de son premier répertoire de travail

Avant de pouvoir profiter de ce merveilleux outil, il faut définir sur quel environnement de travail vous souhaitez l'utiliser.

Il existe plusieurs méthodes pour ce faire, je ne vais pas toutes les citer. Ici, il ne s'agira que de son installation sur un environnement Linux Debian/Ubuntu. Pour les autres systèmes, je vous invite à consulter la [documentation officielle](https://developer.hashicorp.com/terraform/tutorials/aws-get-started/install-cli)

# Installation sur une Debian 11

## Installation des paquets préliminaires 
```bash
sudo apt-get update && sudo apt-get install -y gnupg software-properties-common
```

## Installation de la clef GPG de Hashicorp
```bash
wget -O- https://apt.releases.hashicorp.com/gpg | \
gpg --dearmor | \
sudo tee /usr/share/keyrings/hashicorp-archive-keyring.gpg
```

### Vérification de la clef de sécurité
```bash
gpg --no-default-keyring \
--keyring /usr/share/keyrings/hashicorp-archive-keyring.gpg \
--fingerprint

/usr/share/keyrings/hashicorp-archive-keyring.gpg
-------------------------------------------------
pub   rsa4096 XXXX-XX-XX [SC]
AAAA AAAA AAAA AAAA
uid           [ unknown] HashiCorp Security (HashiCorp Package Signing) <security+packaging@hashicorp.com>
sub   rsa4096 XXXX-XX-XX [E]
```

### Ajout du repository d'Hashicorp
```bash
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] \
https://apt.releases.hashicorp.com $(lsb_release -cs) main" | \
sudo tee /etc/apt/sources.list.d/hashicorp.list
```


### Installation de Terraform 
```bash
sudo apt-get update && sudo apt-get install terraform
```

### Valider l'installation
Une fois l'installation effectuée, nous vérifions que le binaire Terraform est fonctionnel : 
```bash
terraform -help
Usage: terraform [-version] [-help] <command> [args]

The available commands for execution are listed below.
The most common, useful commands are shown first, followed by
less common or more advanced commands. If you're just getting
started with Terraform, stick with the common commands. For the
other commands, please read the help and docs before usage.
```


## Initialiser son premier répertoire de travail
Nous allons créer un dossier et l'initialiser avec le provider de notre étude de cas, [Proxmox](https://registry.terraform.io/providers/Telmate/proxmox/latest/docs) :
```bash
mkdir Terraform
touch provider.tf
```

Voici le contenu du fichier "provider.tf"
```hcl
terraform {
  required_version = ">=1.0.11"

  required_providers {
    proxmox = {
      source = "Telmate/proxmox"
      version = "=2.9.11"
    }
  }
}
```
> Pour des raisons de compatibilité API, je n'utilise pas la dernière version du provider disponible à l'écriture de cet article (2.9.14)
{.is-info}

Une fois le fichier créé, initialisons le dossier afin qu'il puisse télécharger les composants nécessaires afin de communiquer avec l'API Proxmox :

```bash
terraform init
+ terraform init

Initializing the backend...

Initializing provider plugins...
- Finding telmate/proxmox versions matching "2.9.11"...
- Installing telmate/proxmox v2.9.11...
- Installed telmate/proxmox v2.9.11 (self-signed, key ID A9EBBE091B35AFCE)

Partner and community providers are signed by their developers.
If you'd like to know more about provider signing, you can read about it here:
https://www.terraform.io/docs/cli/plugins/signing.html

Terraform has created a lock file .terraform.lock.hcl to record the provider
selections it made above. Include this file in your version control repository
so that Terraform can guarantee to make the same selections by default when
you run "terraform init" in the future.

Terraform has been successfully initialized!

You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.
```

# Conclusion

Dans cet article, nous avons installé Terraform sur notre OS de travail, créer et initialiser un dossier avec le provider correspondant à notre étude de cas.
Dans les prochains articles, nous allons rédiger nos fichiers de déploiements de machine afin de pouvoir déployer notre infrastructure.