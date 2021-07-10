---
title: Ansible - Les modules
description: Utilisation de différents modules Ansible
published: true
date: 2021-07-10T18:14:29.001Z
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

## Paramètres
|--|--|
| `attribute` | Paramètres particuliers d'un fichier : immutabilité etc... (https://fr.wikipedia.org/wiki/Chattr)|
| `force` | Pour les liens symboliques (si le fichier source existe pas, la destination existe)|
| `group/owner` | Propriétaire et groupe de l'élément|
| `mode` | Sous les deux formats : "0755" ou "u=rwx,g=rx,o=rx"|
| `path` | Localisation|
| `recurse` | Création du chemin intermédiaire si n'existe pas (yes/no) > pour directory uniquement|
| `src` | Pour les liens (hard ou symbolique)|
| `state` | Type (absent / directory / file / hard / link / touch)|
    
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
  
## Description
Doc : https://docs.ansible.com/ansible/latest/modules/user_module.html
Commande : `ansible-doc user`
Equivalence : `useradd/adduser/userdel/deluser/luseradd`

## Paramètres
|--|--|
| `append yes/no` | En lien avec groups / ajout aux groupes ou changement |
| `comment` | Commentaire associé au user |
| `create_home yes/no` | Création de la home ou pas |
| `expires format epoch` | Date d'expiration |
| `force` | Permet de forcer la suppression des fichier d'un user |
| `generate_ssh_key` | Génère en même temps une clef ssh à l'utilisateur |
| `group` | Définit le groupe principal de l'utilisateur |
| `groups` | Définit les groupes secondaires qui seront ajoutés |
| `home` | Définition de la home du user |
| `local` | Dans le cas d'une décentralisation de la gestion des users (forcer localement) |
| `move_home` | Pour déplacer une home existante |

## Commandes
