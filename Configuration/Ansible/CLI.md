---
title: Ansible - CLI
description: Comprendre et utiliser les commandes Ansibles
published: true
date: 2021-07-09T07:41:19.292Z
tags: commande, ansible, configuration
editor: markdown
dateCreated: 2021-07-09T07:37:09.124Z
---

![](https://blog.dbi-services.com/wp-insides/uploads/sites/2/2021/03/ansible-logo.png)
# La commande ansible

<div class="video-responsive">
<iframe width="560" height="315" src="https://www.youtube.com/embed/JPohVKUEnbs" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
  </div>

## Informations
- peu utilisé (en proportion) au profit de ansible-playbook
- permettre du test (ping, inventaire)
- permet de jouer des tâches
- beaucoup d'options similaires à la commande ansible-playbook

## Principales options à connaître
* `-u` : user distant utilisé
* `-b` : passer les commandes en élévation de privilèges (sudo)
* `-k` ou `--ask-pass`  > password SSH
* `-K` ou `--ask-become-pass` > password pour élévation privilèges
* `-C` ou `--check` : faire un dry run
* `-D` ou `--diff` : avoir un output de la diff
* `--key-file` : lien direct vers la clef privée
* `-e` ou `--extra-vars` : définir des variables
* `--ask-vault-pass` : déchiffrer un secret vault
* `--vault-password-file` : fichier pour déchiffrer
* `-f x` ou `--forks` : paralléliser
* `-vvv` : verbose

## Commandes utiles
Test de connexions
```bash
ansible -i "node2," all -u vagrant -m ping
```
Définir des paramètres ssh
```bash
ansible -i "node2," all -u vagrant -k --ssh-extra-args="-o 'PreferredAuthentications=password'" -m ping
```

sshpass pour passer un password
```bash
sshpass -p 'vagrant' ansible -i "node2," all -u vagrant -k --ssh-extra-args="-o 'PreferredAuthentications=password'" -m ping
```

Affichage oneline
```bash
ansible -i "node2," all -u vagrant -m ping --one-line
```

Module command
```bash
ansible -i "node2," all -u vagrant -m command -a uptime
```

Passage d'une variable
```bash
ansible -i "node2," all -b -e "var1=xavki" -m debug -a 'msg={{ var1 }}'
```

# Astuces avec les modules
<div class="video-responsive">
<iframe width="560" height="315" src="https://www.youtube.com/embed/blOKHSEs6IY" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
    </div>

## Commandes utiles
Module command
```bash
ansible -i "node2," all -u vagrant -m command -a uptime
```

Module shell
```bash
ansible -i "node2," all -u vagrant -m shell -a "ps aux | grep vagrant | wc -l" --one-line
```

Exemple raw (sans python)
```bash
sudo apt autoremove --purge git
ansible -i "node2," all -u vagrant -b -K -m raw -a "apt install -y git"
```

Module apt
```bash
ansible -i "node2," all -b -m apt -a 'name=nginx state=latest'
```

Arrêt d'un service
```bash
ansible -i "node2," all -b -m service -a 'name=nginx state=stopped'
```
Faire un scp
```bash
ansible -i "node2," all -m copy -a 'src=toto.txt dest=/tmp/titi.txt'
```

Récupérer un fichier
```bash
ansible -i "node2," all -m fetch -a 'src=/tmp/titi.txt dest=xavki.txt flat=yes'
```

Lister les gathers facts
```bash
ansible -i "node2," all -m setup -a "filter=ansible_distribution*"
```