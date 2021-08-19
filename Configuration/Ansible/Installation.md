---
title: Ansible - Installation et configuration
description: Mettre en place Ansible dans son environnement
published: true
date: 2021-07-08T17:52:12.911Z
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

# Configuration et tuning
<div class="video-responsive">
<iframe width="560" height="315" src="https://www.youtube.com/embed/8Hb-i9lXdXA" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
    </div>

## Configuration

Configuration de différentes manières :
- ansible.cfg
- cli

Et à différents endroits pour ansible.cfg (ordre inverse de prise en compte)

- éventuellement en définissant ANSIBLE_CONFIG
- à l'endroit de votre playbook ansible.cfg
- ~/.ansible/ansible.cfg
- /etc/ansible/ansible.cfg

Exemple :
```bash
inventory       = /etc/ansible/hosts
forks           = 5
sudo_user       = root
ask_sudo_pass   = True
ask_pass        = True
gathering       = implicit
gather_subset   = all
roles_path      = /etc/ansible/roles
log_path        = /var/log/ansible.log
vault_password_file = /path/to/vault_password_file
fact_caching_connection =/tmp
pipelining = False
```
Doc : https://docs.ansible.com/ansible/2.3/intro_configuration.html

Commande :
```bash
ansible-config
ansible-config view  # voir le ansible.cfg pris en compte
ansible-config list  # toute les variables et leurs valeurs
ansible-config dump  # liste toutes les variables ansible
ansible-config dump --only-changed #valeurs par défaut modifiée
```
cf : https://docs.ansible.com/ansible/latest/reference_appendices/config.html

Exemple :
```bash
ANSIBLE_SSH_ARGS:
  default: -C -o ControlMaster=auto -o ControlPersist=60s
  description:
  - If set, this will override the Ansible default ssh arguments.
  - In particular, users may wish to raise the ControlPersist time to encourage performance.  A
    value of 30 minutes may be appropriate.
  - Be aware that if `-o ControlPath` is set in ssh_args, the control path setting
    is not used.
  env:
  - name: ANSIBLE_SSH_ARGS
  ini:
  - key: ssh_args
    section: ssh_connection
  yaml:
    key: ssh_connection.ssh_args
```

## Tuning
Host key checking = fingerprint
```bash
[defaults]
host_key_checking = False
```

Callback temps par action
```bash
[defaults]
callback_whitelist = profile_tasks
```

Pipelining
```bash
[ssh_connection]
pipelining = True
```

Principe par défaut :
- création fichier python
- création directory
- envoi fichier python via sftp
- run python
- récupération résultat

Avec pipelining :
- génération du fichier python
- envoi sur le python interpreter distant via stdin
- récupération du stdout

Rq: travailler sans fichier distant

Partage de plusieurs sessions et augmentation de la persistence (connexion...)
```bash
[ssh_connection]
ssh_args = -o ControlMaster=auto -o ControlPersist=60s
```
Doc : https://www.blog-libre.org/2019/05/11/loption-controlmaster-de-ssh_config/


Spécifier le mode d'identification
```bash
[ssh_connection]
ssh_args = -o ControlMaster=auto -o ControlPersist=60s -o PreferredAuthentications=publickey
```

fork = parallélisation
```bash
[defaults]
forks = 30
```

gather facts avec précaution
```bash
gather_facts: no
```

gather facts caching par fichier
```bash
fact_caching = jsonfile
fact_caching_timeout = 3600
fact_caching_connection = /tmp/mycachedir
```

gather facts caching par redis
```bash
fact_caching = redis
fact_caching_timeout = 3600
fact_caching_connection = localhost:6379:0
```

Mitogen

Doc : https://mitogen.networkgenomics.com/ansible_detailed.html


cas ultime > ansible localhost  >> ansible-pull (commande)

chargement du code ansible sur le serveur distant

cloud init > cron > ansible-pull


exécution en localhost
problème récupération des informations
