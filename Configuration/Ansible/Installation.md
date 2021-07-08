---
title: Ansible - Installation et configuration
description: Mettre en place Ansible dans son environnement
published: true
date: 2021-07-08T17:18:58.350Z
tags: ansible, configuration
editor: markdown
dateCreated: 2021-07-08T17:18:58.350Z
---

![](https://blog.dbi-services.com/wp-insides/uploads/sites/2/2021/03/ansible-logo.png)

# Installation
<div class="video-responsive">
  <iframe width="560" height="315" src="https://www.youtube.com/embed/0P25aOuUyxY" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
  </div>

## Liens utiles

Documentation : https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html

Les différentes releases : https://releases.ansible.com/ansible/


## Prérequis
### Controller node
* Python >= 2.7
* tout sauf windows
* ssh/scp (ou sftp)


### Managed node
* Python >= 2.6




## Différents types d'installations
* paquets des distributions
* librairie python
* binaire
* éventuellement par docker (abandonné depuis 2 ans)

## Installation via le binaire
```bash
git clone https://github.com/ansible/ansible.git
cd ansible
source ./hacking/env-setup
sudo apt install python-pip
pip install --user -r ./requirements.txt
echo "127.0.0.1" > ~/ansible_hosts
export ANSIBLE_INVENTORY=~/ansible_hosts
ansible all -m ping --ask-pass
```


## Installation via pip
```bash
sudo apt install python3-pip
pip3 install ansible
```

## Installation via les paquets (Debian)
```bash
sudo apt install ansible
```  

## Remarques
### MacOS
* remonter la limite du nombre de fichiers
* fork > 15
```bash
sudo launchctl limit maxfiles unlimited
```  

### Python interpreter - par défaut /usr/bin/python
```bash
ansible_python_interpreter=/usr/bin/python3
``` 

ou installation (raw ne dépend pas de python côté client)
```bash
ansible myhost --become -m raw -a "yum install -y python2"
``` 
Doc : https://docs.ansible.com/ansible/latest/user_guide/intro_inventory.html#ansible-python-interpreter
