---
title: Ansible - Les modules
description: Utilisation de différents modules Ansible
published: true
date: 2021-07-12T08:06:11.099Z
tags: ansible, configuration, module
editor: markdown
dateCreated: 2021-07-09T15:18:02.744Z
---

![](https://blog.dbi-services.com/wp-insides/uploads/sites/2/2021/03/ansible-logo.png)

# LISTES DES MODULES
- [🌀 1 - FILE *Gestion des fichiers et répertoires*](#h-1-file-directory-softlink-droits-touch)
- [🌀 2 - USER *Gestion des utilisateurs*](#h-2-user-creer-supprimer-gerer-des-utilisateurs)
- [🌀 3 - REGISTER & STAT ](#h-3-register-stat)
- [🌀 4 - APT *Gestion des paquets*](#h-4-apt-installation-gestion-suppression-de-paquets)
- [🌀 5 - REBOOT *Redémarrer un serveur*](#h-5-reboot-redemarrer-un-serveur-apres-upgrade)
- [🌀 6 - SSH *Création et déploiement de clefs*](#h-6-ssh-creation-et-deploiement-de-clefs)
- [🌀 7 - COPY *Gestion des copies, des backups avec vérifications, etc..*](#h-7-copy-validate-backup-recurse)
- [🌀 8 - FETCH *Récupération de données*](#h-8-fetch-recuperer-des-fichiers)
- [🌀 9 - TEMPLATE *génération de fichier à partir de modèles intégrant des variables*](#h-9-template-les-bases-variables-listes-boucles-astuces)
{.links-list}

# 1 - FILE : DIRECTORY, SOFTLINK, DROITS, TOUCH...
<div class="video-responsive">
<iframe width="560" height="315" src="https://www.youtube.com/embed/EmivDVqBqHw" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
  </div>
  
## Description

Documentation : https://docs.ansible.com/ansible/latest/modules/file_module.html
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
---
# 2 - USER : CREER, SUPPRIMER, GERER DES UTILISATEURS
<div class="video-responsive">
<iframe width="560" height="315" src="https://www.youtube.com/embed/O9KOT2jxBu4" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
  </div>
  
## Description
Documentation : https://docs.ansible.com/ansible/latest/modules/user_module.html
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
| `name` | nom utilisateur |
| `password` | Hash du password |
| `password_lock` | Vérouiller le password du user |
| `remove` | En cas de state absent, suppression en même des répertoires du user |
| `shell` | Définition sur shell attribué au user |
| `skeleton` | Avec create_home, pour définir le squelette à appliquer |
| `ssh_key_bits` | Taille de la clef ssh générée |
| `ssh_key_comment` | Commentaire de la clef ssh |
| `ssh_key_file` | Spécifie le chemin de la clef ssh |
| `ssh_key_passphrase`| Définir la passphrase de la clef ssh sinon pas de passphrase |
| `ssh_key_type` | rsa par défaut, type de clef ssh |
| `state` | Création ou suppression |
| `system` | A la création définir ou non un compte system |
| `uid` | Fixer l'uid |
| `update_password always/on_create` | Soit mettre à jour sur changement ou juste création

## Commandes
Création d'un user avec password
```yaml
- name: création de xavki
  user:
    name: xavki
    state: present
    password: "{{ 'password' | password_hash('sha512') }}"   
```

Ajout au groupe sudo
```yaml
- name: création de xavki
  user:
    name: xavki
    state: present
    groups: sudo
    append: yes
    password: "{{ 'password' | password_hash('sha512') }}"   
```

Fixer l'uid
```yaml
- name: création de xavki
  user:
    name: xavki
    state: present
    uid: 1200
    groups: sudo
    append: yes
    password: "{{ 'password' | password_hash('sha512') }}" 
```
Génération de la clef ssh
```yaml
- name: création de xavki
  user:
    name: xavki
    state: present
    uid: 1200
    groups: sudo
    append: yes
    generate_ssh_key: yes
    password: "{{ 'password' | password_hash('sha512') }}"   
```

Ajout d'un register et découvrir les outputs
```yaml
  - name: création du user xavki
    user:
      name: xavki
      state: present
      generate_ssh_key: yes
      uid: 1200
      groups: sudo
      append: yes
      password: "{{ 'password' | password_hash('sha512') }}"
    register: mavar
  - name: debug
    debug:
      msg: "{{ mavar }}"
```

nologin avec le shell
```yaml
  - name: création du user xavki
    user:
      name: xavki
      state: present
      shell: /sbin/nologin
      generate_ssh_key: yes
      uid: 1200
      groups: sudo
      append: yes
      password: "{{ 'password' | password_hash('sha512') }}"
      password_lock: yes
```

Suppression d'un user
```yaml
  - name: création du user xavki
    user:
      name: xavki
      state: absent
```
---
# 3 - REGISTER & STAT
<div class="video-responsive">
<iframe width="560" height="315" src="https://www.youtube.com/embed/yI8duHq9HMY" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
  </div>
  
## Description

Documentation : https://docs.ansible.com/ansible/latest/modules/stat_module.html


## Paramètres
|--|--|
| `path` | Chemin du fichier ou répertoire |
| `follow` | Suivre les liens symboliques |
| `get_checksum` | Récupérer la checksum |
| `checksum_algorithm` | Type de checksum (md5, etc) |
| `get_mime` | Récupération du type de données |

## Commandes
Création d'un fichier
```yaml
  - name: création d'un fichier
    file:
      path: /tmp/xavki.txt
      state: touch
      owner: xavki
```

Utilisation de stat
```yaml
  - name: check avec stat
    stat:
      path: /tmp/xavki.txt
```

Récupération de la variable
```yaml
  - name: check avec stat
    stat:
      path: /tmp/xavki.txt
    register: __fichier_xavki_exist
  - name: debug
    debug:
      var: __fichier_xavki
```

Récupération d'une clef
```yaml
  - name: debug
    debug:
      var: __fichier_xavki.stat.exists
```

Utilisation conditionnnel
```yaml
  - name: création répertoire xavki
    file:
      path: /tmp/xavki
      state: directory
    when: __fichier_xavki.stat.exists
```

## Exemple
```yaml
  tasks:
  - name: création d'un fichier
    file:
      path: /tmp/xavki.txt
      state: touch
      owner: root
    when: xavki_file is defined
  - name: check avec stat
    stat:
      path: /tmp/xavki.txt
    register: __fichier_xavki
  - name: debug
    debug:
      var: __fichier_xavki.stat.exists
  - name: création répertoire xavki
    file:
      path: /tmp/xavki
      state: directory
    when: __fichier_xavki.stat.exists and xavki_file is defined
```
---
# 4 - APT : INSTALLATION, GESTION, SUPPRESSION DE PAQUETS
<div class="video-responsive">
<iframe width="560" height="315" src="https://www.youtube.com/embed/0Capz3Z6Fds" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
  </div>
  
## Description
Documentation : https://docs.ansible.com/ansible/latest/collections/ansible/builtin/apt_module.html
Commande : `apt`

## Paramètres
|--|--|
| `allow_unauthenticated` | Autoriser l'installation de paquets non authentifiés |
| `autoclean` | Effacement des anciennes versions des paquets |
| `cache_valid_time` | Durée durant laquelle ne pas remettre à jour le cache apt |
| `deb` | Lien vers une source de paquet .deb |
| `default_release` | Version par défaut |
| `dpkg_options` | Option d'installation dpkg |
| `force` | Equivaut à --force-yes, désactive la signature et certificats de paquets |
| `force_apt_get` | Force l'utilisation de apt-get |
| `install_recommends` | Activer ou désactiver les paquets recommandés (dépend des OS) |
| `name` | Nom du paquet |
| `only_upgrade` | Met à jour uniquement les paquets installés |
| `policy_rc_d` | Règle de déclenchement automatique à l'installation d'un paquet |
| `purge` | Purge les fichiers de configurations (--purge) |
| `state present / absent / latest / fixed / build-dep` | Permet de choisir le status voulu |
| `update_cache` | Réaliser un update avant l'installation |
| `update_cache_retries` | Nombre de tentatives de l'update |
| `update_cache_retry_max_delay` | Délai de chaque retry |
| `upgrade : yes / no / safe / dist / full` | |

## Commandes
Mise à jour du cache
```yaml
  - name:
    apt:
      update_cache: yes
      cache_valid_time: 3600
```

Délai de validité du cache
```yaml
  - name:
    apt:
      name: haproxy
      update_cache: yes
      cache_valid_time: 60
```

Utiliser la version backport
```yaml
  - name:
    apt:
      name: haproxy
      default_release: stretch-backports
      update_cache: yes
      cache_valid_time: 60
```
> Requière : `apt list -a haproxy` et `apt list -i haproxy`
{.is-warning}

Mise à jour
```yaml
  - name:
    apt:
      name: haproxy
      update_cache: yes
      cache_valid_time: 60
      state: latest
```

Suppression
```yaml
  - name:
    apt:
      name: haproxy
      state: absent
```

Suppression complète
```yaml
  - name:
    apt:
      name: haproxy
      state: absent
      purge: yes
      autoremove: yes
```
---
# 5 - REBOOT : REDEMARRER UN SERVEUR APRES UPGRADE
<div class="video-responsive">
<iframe width="560" height="315" src="https://www.youtube.com/embed/SRD2h5Fh4fA" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
  </div>
  
## Description
Documentation : https://docs.ansible.com/ansible/latest/collections/ansible/builtin/reboot_module.html
Objectifs : rebooter des machines sous conditions et reprendre après

## Paramètres
|--|--|
| `boot_time_command` | Command qui génère l'id de reboot |
| `connect_timeout` | Timeout de la connexion en seconde |
| `msg` | Message délivré avant le reboot |
| `post_reboot_delay` | Temps d'attente en secondes après le reboot pour continuer |
| `pre_reboot_delay` | Délai avant de lancer le reboot |
| `reboot_timeout` | Timeout du reboot lui-même |
| `search_paths` | Path pour la commande shutdown |
| `test_command` | Commande de test pour confirmer le succés du reboot |

## Commandes

Démonstration avec de simples fichiers

```yaml
- name: mon premier playbook
  hosts: all
  remote_user: vagrant
  become: yes
  tasks:
  - name: create file
    file:
      path: /tmp/xavki.txt
      state: touch
```

```yaml
  - name: test
    stat:
      path: /tmp/xavki.txt
    register: __file_exist
```

```yaml
  - name: lancement du reboot avec reboot
    reboot:
      msg: "Reboot via ansible"
      connect_timeout: 5
      reboot_timeout: 300
      pre_reboot_delay: 0
      post_reboot_delay: 30
      test_command: uptime
    when: __file_exist.stat.exists

  - name: file2
    file:
      path: /tmp/xavki2.txt
      state: touch
```

## Exemple
Mise à jours
```yaml
    - name: update cache
      apt:
        update_cache: yes
        force_apt_get: yes
        cache_valid_time: 3600

    - name: upgrade général
      apt:
        upgrade: dist
        force_apt_get: yes

    - name: vérification à partir du fichier reboot_required
      register: reboot_required_file
      stat:
        path: /var/run/reboot-required

    - name: lancement du reboot avec reboot
      reboot:
        msg: "Reboot via ansible"
        connect_timeout: 5
        reboot_timeout: 300
        pre_reboot_delay: 0
        post_reboot_delay: 30
        test_command: uptime
      when: reboot_required_file.stat.exists
``` 
---
# 6 - SSH : CREATION ET DEPLOIEMENT DE CLEFS
<div class="video-responsive">
<iframe width="560" height="315" src="https://www.youtube.com/embed/NZX80E0Uyao" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
  </div>
  
## Description
Documentation : 
 - https://docs.ansible.com/ansible/latest/collections/ansible/posix/authorized_key_module.html
 - https://docs.ansible.com/ansible/latest/collections/community/crypto/openssh_keypair_module.html

Objectifs : générer une clef ssh et la déployer
Attention : où suis-je ? qui suis-je ?

## Paramètres openssh_keypair
|--|--|
| `attibutes` | Attributs des fichiers (non supprimable etc) |
| `comment` | Commentaire de la clef |
| `force` | Regénère la clef si elle existe déjà |
| `group` | Groupe propriétaire des fichiers |
| `mode` | Permisssions (cf file, 0600, u+rw) |
| `owner` | Propirétaire |
| `path` | Localisation des clefs (attention sécurité de la clef privée) |
| `regenerate` | never / fail / partial_idempotence (si non conforme) / full_idempotence (et si non lisible) / always |
| `size` | Taille de la clef |
| `state` | present/absent |
| `type` | rsa / dsa / rsa1 / ecdsa / ed25519 |
| `unsafe_writes` | Prévenir les corruptions de fichiers |

## Paramètres authorized_key
|--|--|
| `comment` | Commentaire (écrase le commentaire d'origine) |
| `exclusive` | Permet de supprimer les clefs non mentionnée (plusieurs clefs possibles) |
| `follow` | Suivre les liens symboliques |
| `key` | Contenu de la clef (cf lookup) |
| `key_options` | Options de la clef ssh (from=<ip>...) |
| `manage_dir` | Gère lui-même le répertoire (création etc...) - default yes |
| `path` | Chemin alternatif vers la clef (default .ssh...) |
| `state` | present / absent |
| `user` | Utilisateur de la machine cible où sera installé la clef |


## Exemple 
Génération de clef

``` yaml
- name: mon premier playbook
  hosts: all
  remote_user: vagrant
  tasks:
  - name: generate SSH key"
    openssh_keypair:
      path: /tmp/xavki
      type: rsa
      size: 4096
      state: present
      force: no
    run_once: yes
    delegate_to: localhost
```
Création d'un utilisateur et déploiement de sa clé   
```yaml
  - name: création du user devops
    user:
      name: devops
      shell: /bin/bash
      groups: sudo
      append: yes
      password: "{{ 'password' | password_hash('sha512') }}"
    become: yes

  - name: Add devops user to the sudoers
    copy:
      dest: "/etc/sudoers.d/devops"
      content: "devops  ALL=(ALL)  NOPASSWD: ALL"
    become: yes

  - name: Deploy SSH Key
    authorized_key: 
      user: devops
      key: "{{ lookup('file', '/tmp/xavki.pub') }}"
      state: present
    become: yes
```

Mode exclusif

```yaml
- name: Set authorized key, removing all the authorized keys already set
  authorized_key:
    user: root
    key: '{{ item }}'
    state: present
    exclusive: True
  with_file:
    - public_keys/doe-jane
```
---
# 7 - COPY : VALIDATE, BACKUP, RECURSE...
<div class="video-responsive">
<iframe width="560" height="315" src="https://www.youtube.com/embed/oIxoRcccnZ8" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
</div>
  
## Description
Documentation: https://docs.ansible.com/ansible/2.5/modules/copy_module.html
Objectifs : copier des fichiers ou du contenu
Equivalent : scp
  
## Paramètres
|--|--|
| `attributes` | Attributs du fichier |
| `backup` | Réalise une copie datée avant la copie |
| `checksum` | Vérification du ficheir via un hash |
| `content` | Dans le cas ou la source n'est pas un fichier mais une variable ou un string |
| `decrypt` | Déchiffre les fichiers si ils sont vaultés (défaut : yes) |
| `dest` | Localisation du fichier sur les serveurs target |
| `directory_mode` | Dans le cas d'une recopie en mode récursif |
| `follow` | Indiquer le filesytème dans la destination |
| `force` | Remplace le fichier si il est différent de la source |
| `group` | Group propriétaire |
| `local_follow` | Indique le filesystème dans la source |
| `mode` | Permissions du fichier ou du répertoire (0755, u+rwx,g+rx,o+rx) |
| `owner` | User propriétiare |
| `remote_src` | Copie du master vers la target, yes > copie de la target vers la target |
| `src` | Localisation de la source |
| `unsafe_writes` | Eviter la corruption de fichier |
| `validate` | Commande jouée pour valider le fichier avant de le copier (le fichier se situe %s) |

  ## Commandes
Simple
```yaml
  tasks:
  - name: copy
    copy:
      src: test.txt
      dest: /tmp/xavki.txt
```

> Attention à la localisation de la source (cf les rôles)
{.is-warning}



Si changement > de base reupload
```yaml
  tasks:
  - name: copy
    copy:
      src: test.txt
      dest: /tmp/xavki.txt
      force: no
```

En mode récursif
```bash
mkdir -p tmp/xavki/{1,2,3}
```

```yaml
  - name: copy
    copy:
      src: tmp/
      dest: /tmp/
```

Déplacer les fichiers ou répertoires sur la cible
```yaml
  - name: copy
    copy:
      src: /home/oki
      dest: tmp/
      remote_src: yes
```

Combinaison avec with_items
```yaml
  vars:
    mesfichiers:
      - { source: "xavki1.txt", destination: "/tmp/{{ ansible_hostname }}_xavki1.txt", permission: "0755" }
      - { source: "xavki2.txt", destination: "/home/oki/{{ ansible_hostname }}_xavki2.txt", permission: "0644" }
  tasks:
  - name: copy
    copy:
      src: "{{ item.source }}"
      dest: "{{ item.destination }}"
      mode: "{{ item.permission }}"
    with_items: "{{ mesfichiers }}"
```

Utilisation de pattern
```yaml
  - name: copy
    copy:
      src: "{{ item }}"
      dest: /tmp/
    with_fileglob:
    - xavk*
```

Avec backup
```yaml
  - name: copy
    copy:
      src: "{{ item }}"
      dest: /tmp/
      backup: yes
    with_fileglob:
    - xavk*
```

Recopier du contenu à partir d'une variale ou/et un string
```yaml
  - name: copy
    copy:
      content: |
         Salut
         la team !!
         on est sur {{ ansible_hostname }}
      dest: /tmp/hello.txt
```

Avec validation
```yaml
- name: copie du fichier nginx.conf avec check
  copy:
    src: nginx.conf
    dest: /etc/nginx/nginx.conf
    owner: root
    group: root
    mode: 0644
    validate: /usr/bin/nginx -t -c %s
```

Ou encore
```yaml
  - name: Add devops user to the sudoers
    copy:
      dest: "/etc/sudoers.d/devops"
      content: "oki ALL=(ALL)  NOPASSWD: ALL"
      owner: root
      group: root
      mode: 0400
      validate: /usr/sbin/visudo -cf %s
```

Test
```yaml
  - name: Add devops user to the sudoers
    copy:
      dest: "/etc/sudoers.d/devops"
      content: "oki ALL=(ALL)  AAAAA: ALL"
      owner: root
      group: root
      mode: 0400
      validate: /usr/sbin/visudo -cf %s
    become: yes
```
---
# 8 - FETCH : RECUPERER DES FICHIERS
  <div class="video-responsive">
<iframe width="560" height="315" src="https://www.youtube.com/embed/hYsI7_259kQ" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
</div>
  
## Description
Documentation: https://docs.ansible.com/ansible/latest/collections/ansible/builtin/fetch_module.html
Objectifs : récupération de fichiers des machines target
Equivalent : scp
  
## Paramètres
|--|--|
| `dest` | Destination du fichier récupérer (sur la machine ansible) |
| `fail_on_missing` | Erreur si le fichier manque (défaut yes) |
| `flat` | Ecraser/force la destination (attention si plusieurs hosts) |
| `src` | Fichier récupéré de la machine target |
| `validate_checksum` | Validation de la récupération via la checksum |
  
## Commandes
Exemple simple si copie vers \/
```yaml
  - name: fetch
    fetch:
      src: /etc/hosts
      dest: tmp/
```
Avec une destination précise (attention sans flat)
```yaml
  - name: fetch
    fetch:
      src: /etc/hosts
      dest: tmp/hosts_{{ ansible_hostname }}.txt
```

Avec le flat
```yaml
  - name: fetch
    fetch:
      src: /etc/hosts
      dest: tmp/hosts_{{ ansible_hostname }}.txt
      flat: yes
```
  
## Exemples
Collecter les fichiers et les mettre à dispo avec nginx

```yaml
- name: preparation local
  connection: local
  hosts: localhost
  become: yes
  tasks:
  - name: install nginx
    apt:
      name: nginx
      state: present
  - name: clean
    file:
      path: "{{ item }}"
      state: absent
    with_fileglob:
    - /var/www/html/*.html
- name: on découvre copy
  hosts: all
  tasks:
  - name: fetch
    fetch:
      src: /etc/hosts
      dest: /var/www/html/hosts_{{ ansible_hostname }}.txt
      flat: yes
```

Configuration nginx :

```bash
  autoindex on;
  autoindex_exact_size off;
```

---
# 9 - TEMPLATE : LES BASES, VARIABLES, LISTES, BOUCLES, ASTUCES...
<div class="video-responsive">
<iframe width="560" height="315" src="https://www.youtube.com/embed/bprLiIL0BA4" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
</div>
  
## Description
Documentation: https://docs.ansible.com/ansible/latest/collections/ansible/builtin/template_module.html
Objectifs : génération de fichier à partir de modèles intégrant des variables
format jinja2 (python)
  
## Paramètres
|--|--|
| `attributes` | Attributs de fichiers |
| `backup` | Créé une sauvegarde avant la modification (idem copy) |
| `block_end_string` | Fin de block dans les templates ( %}) |
| `block_start_string` | Début de block ({%) |
| `dest` | Fichier cible ou généré |
| `follow` | Suivre les liens symboliques |
| `force` | Ecraser si le fichier de destination existe (défaut : yes) |
| `group` | Groupe propriétaire du fichier |
| `lstrip_blocks` | Respect stricte ou non des tabulations et blancs |
| `mode` | Permissions du fichier (0755 ou r+rwx,g+rx,o+rx) |
| `newline_sequence` | Quel élément est utilisé pour les nouvelles lignes |
| `output_encoding` | Encodage du fichier généré (defaut : utf8) |
| `owner` | Propriétaire du fichier |
| `src` | Fichier source (template), attention localisation |
| `trim_blocks` | Supprimer les retours à la ligne des blocks |
| `unsafe_writes` | Eviter la corruption des fichiers |
| `validate` | Commande de validation avant modification (idem copy) |
| `variable_end_string` | Caractères des fins des variables |
| `variable_start_string` | Caractères de début des variables  |
  
## Commandes
Le plus simplte : 
```yaml
- name: preparation local
  hosts: all
  vars:
    var1: "Xavier !!!"
  tasks:
  - name: template
    template:
      src: montemplate.txt.j2
      dest: /tmp/hello.txt
```

Avec comme template :

```json
Hello {{ var1 }}
````
  
Quelques variables utiles

```yaml
    ansible_managed : pour afficher en début de chaque template (prévenir)
    template_host : machine qui a joué le template
    template_uid : user à l'origine de la modification via le template
    template_path : localisation du fichier
    template_fullpath : chemin complet
    template_run_date : date de modification

```

Exemple au début du fichier

```json
#{{ template_run_date }} - "{{ ansible_managed }}" via {{ template_uid }}@{{ template_host }}

```

Permissions, user et group

```yaml
  - name: template
    template:
      src: montemplate.txt.j2
      dest: /tmp/hello.txt
      owner: oki
      group: oki
      mode: 0755
```

Le backup avant modification


```yaml
  - name: template
    template:
      src: montemplate.txt.j2
      dest: /tmp/hello.txt
      owner: oki
      group: oki
      mode: 0755
      backup: yes
```

Parcourir une liste > un fichier par itération

```yaml
  vars:
    var1: "Xavier !!!"
    var2:
      - { nom: "xavier", age: "40" }
      - { nom: "paul", age: "22" }
      - { nom: "pierre", age: "25" }
  tasks:
  - name: template
    template:
      src: montemplate.txt.j2
      dest: "/tmp/hello_{{ item.nom }}.txt"
    with_items:
    - "{{ var2 }}"
```

Template :

```json
#{{ template_run_date }} - "{{ ansible_managed }}" via {{ template_uid }}@{{ template_host }}
Hello {{ var1 }}
je suis {{ item.nom }}
j'ai {{ item.age }}
```

Itération dans le template

```yaml
  vars:
    var1: "Xavier !!!"
    var2:
      - { nom: "xavier", age: "40" }
      - { nom: "paul", age: "22" }
      - { nom: "pierre", age: "25" }
  tasks:
  - name: template
    template:
      src: montemplate.txt.j2
      dest: "/tmp/hello_all.txt"
```

Template :

```json
#{{ template_run_date }} - "{{ ansible_managed }}" via {{ template_uid }}@{{ template_host }}
Hello {{ var1 }}
{% for personne in var2 %}
    je suis {{ personne.nom }}
    j'ai {{ personne.age }}
{% endfor %}
```


