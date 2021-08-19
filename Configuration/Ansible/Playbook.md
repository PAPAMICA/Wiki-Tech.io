---
title: Ansible - Les playbooks
description: Découverte des playbooks Ansible
published: true
date: 2021-07-09T14:38:55.362Z
tags: ansible, configuration
editor: markdown
dateCreated: 2021-07-09T14:38:53.380Z
---

![](https://blog.dbi-services.com/wp-insides/uploads/sites/2/2021/03/ansible-logo.png)

# Premiers pas et options
<div class="video-responsive">
  <iframe width="560" height="315" src="https://www.youtube.com/embed/yN29WlhIUrI" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
  </div>

## Introduction
Playbook :
* fichier déclenchant les actions à réaliser
* sert à articuler l'inventory avec les rôles
* peut inclure des tasks (actions) > éviter
* peut inclure des variables (éviter autant que possible)
* peut faire tout ce que fait un rôle (globalement) > rôle
* spécifier quel user et comment ?

Une commande : `ansible-playbook`


## La commande et les options
Une commande : `ansible-playbook`

* `-i` : inventory
* `-l` : limit > spécifier un/des groupes ou serveurs ou patterns
* `-u` : user
* `-b` : become > sudo
* `-k` : password de ssh (à éviter)
* `-K` : password du sudo
* `-C` : check > dry run
* `-D` : diff > afficher les différences avant/après les tasks (actions)
* `--ask-vault` : prompt pour le password vault
* `--syntax-check` : vérfier la syntax
* `--vault-password-file` : passer le vault password par un fichier
* `-e` : surcharger n'importe quelle variable
* `-f` : nombre de parallélisation
* `-t` : filtrer sur les tags (--skip-tags)
* `--flush-cache` : éviter l'utilisation du cache
* `--step` : une tâche à la fois (confirmation via prompt)
* `--start-at-task` : commencer à une tâche spécifiquement
* `--list-tags` : lister tous les tags rencontrés
* `--list-tasks` : liste les tâches qui vont être exécutées

## Exemple
```yaml
- name: Mon Playbook !!
  hosts: all
  remote_user: vagrant
  become_user: yes
  tasks:
  - name: je debug
    debug:
      msg: "{{ var1 }}"
```
