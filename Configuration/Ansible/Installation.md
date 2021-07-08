---
title: Ansible - Installation et configuration
description: Mettre en place Ansible dans son environnement
published: true
date: 2021-07-08T17:36:05.922Z
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

# Configuration SSH

<div class="video-responsive">
<iframe width="560" height="315" src="https://www.youtube.com/embed/dpDQ94huxmI" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
    </div>

## Principes clefs
* clef privée
* clef privée
* type de clef / algorithme (rsa, dsa, ecdsa)
* longeur de clef (dépend de l'algo ecdsa 521)

## Gestion d'une clef SSH

### Génération via ssh-keygen
```bash
ssh-keygen -t ecdsa -b 521
```

Spécifier la localisation de sortie
```bash
ssh-keygen -t ecdsa -b 521 -f /myhome/.ssh/maclefprivee
```

> Configurez une passphrase sinon une clef ssh est plus dangereuse qu'un password
{.is-warning}

### Ajout de votre clef publique sur le host distant
```bash
vim /home/user/.ssh/authorized_keys
```
Ou via ssh-copy-id
```bash
ssh-copy-id -i /myhome/.ssh/maclefprivee xavki@monhost
``` 

Remarque : specific pour une ip
```bash
from="10.0.0.?,*.example.com",no-X11-forwarding ssh-rsa AB3Nz...EN8w== xavki@monhost
```


### Utilisation de la clef
```bash
ssh -i /localisation/clef/privee xavki@monhost
```

Ou plus facilement par un agent ssh (embarque votre configuration ssh)

Check si un agent tourne
```bash
ps -p $SSH_AGENT_PID
```
Lancement d'un agent ssh
```bash
eval `ssh-agent`
```
Ajout de la clef à l'agent
```bash
ssh-add
```
Check de la clef de l'agent
```bash
ssh-add -l
```

Exemple :
```bash
touch ~/.ssh/config
chmod 600 ~/.ssh/config
cat ~/.ssh/config

Host * !monhost*
    User vagrant
    Port 22
    IdentityFile /myhome/.ssh/maclefprivee
    LogLevel INFO
    Compression yes
    ForwardAgent yes
    ForwardX11 yes
```

Astuce pour bypasser la conf
```bash
ssh -F /dev/null xavki@monhost
```