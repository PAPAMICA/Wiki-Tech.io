---
title: Ansible - Les modules
description: Utilisation de différents modules Ansible
published: true
date: 2021-07-09T15:21:40.124Z
tags: ansible, configuration, module
editor: markdown
dateCreated: 2021-07-09T15:18:02.744Z
---

![](https://blog.dbi-services.com/wp-insides/uploads/sites/2/2021/03/ansible-logo.png)

# FILE : DIRECTORY, SOFTLINK, DROITS, TOUCH...
<div class="video-responsive">
<iframe width="560" height="315" src="https://www.youtube.com/embed/EmivDVqBqHw" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
  </div>
  
## Description

Doc : https://docs.ansible.com/ansible/latest/modules/file_module.html
Commande : `ansible-doc file`

Objectif : gestion des fichiers et répertoires

Périmètre : fichiers, répertoires, liens symboliques

Options courantes :

* `attribute` : paramètres particuliers d'un fichier : immutabilité etc...
							(https://fr.wikipedia.org/wiki/Chattr)

* `force` : pour les liens symboliques (si le fichier source existe pas, la destination existe)

* `group/owner` : propriétaire et groupe de l'élément

* `mode` : sous les deux formats : "0755" ou "u=rwx,g=rx,o=rx"

* `path` : localisation

* `recurse` : création du chemin intermédiaire si n'existe pas (yes/no) > pour directory uniquement

* `src` : pour les liens (hard ou symbolique)

* `state` : type (absent / directory / file / hard / link / touch)
	touch > créé le fichier vide
		file > vérifie l'existence et les caractéristiques
    
## Commandes
Créer un répertoire
```yaml
- name: création du répertoire /tmp/xavki
  file:
    path: /tmp/xavki/
    state: directory
```
> Le propriétaire du dossier et l'utilisateur de connexion
{.is-info}


Changement de user > root
```yaml
- name: création du répertoire /tmp/xavki
  file:
    path: /tmp/xavki/
    state: directory
    owner: root
```

become = yes > différents endroits pour le faire
* cli > -b
* playbook/tasks > become: yes
```yaml
- name: création du répertoire /tmp/xavki
  file:
    path: /tmp/xavki/
    state: directory
    owner: root
  become: yes
```
> Attention à l'indentation
{.is-warning}

Modification du groupe et des droits (RWX-RX-RX - 0755) | stat
```yaml
- name: création du répertoire /tmp/xavki
  file:
    path: /tmp/xavki/
    state: directory
    owner: root
    group: root
    mode: 0755
  become: yes
```

Récursivité (pour directory uniquement
```yaml
  - name: création du répertoire /tmp/xavki
    file:
      path: /tmp/xavki/1/2/3/4
      recurse: yes
      state: directory
      owner: root
      group: root
      mode: 0755
```
Touch
```yaml
  - name: création du répertoire /tmp/xavki
    file:
      path: /tmp/xavki/1/2/3/4/fichier.txt
      state: touch
      owner: root
      group: root
      mode: 0755
```
Lien symbolique = lien vers fichier (diff avec hardlink = lien vers inode)
```yaml
  - name: création du répertoire /tmp/xavki
    file:
      src: /tmp/xavki/1/2/3/4/
      dest: /tmp/symlink
      state: link  #hard
      owner: root
      group: root
      mode: 0755
```


Suppression de fichier
```yaml
  - name: dir sans idempotence
    file:
      path: /tmp/xavki.txt
      state: absent
```

Suppression de répertoire récursive
```yaml
  - name: dir sans idempotence
    file:
      path: /tmp/xavki/
      state: absent
```

# USER : CREER, SUPPRIMER, GERER DES UTILISATEURS
<div class="video-responsive">
<iframe width="560" height="315" src="https://www.youtube.com/embed/O9KOT2jxBu4" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
  </div>