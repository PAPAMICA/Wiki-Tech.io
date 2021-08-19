---
title: Ansible - Les inventaires
description: Créer et gérer les inventaires Ansible
published: true
date: 2021-07-09T14:10:01.810Z
tags: ansible, configuration
editor: markdown
dateCreated: 2021-07-09T08:01:35.009Z
---

![](https://blog.dbi-services.com/wp-insides/uploads/sites/2/2021/03/ansible-logo.png)
# La struturation
<div class="video-responsive">
<iframe width="560" height="315" src="https://www.youtube.com/embed/D8BUV1RYFjw" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
  </div>
  
## Informations
inventory = inventaire des machines et de leurs variables
inventory =
* fichier d'inventaire
* répertoire group_vars
* répertoire host_vars

**Eléments éssentiels car il décrit votre infra :**
* vos serveurs
* vos types de serveurs

**Deux types d'instances :**
* hosts
* groupes

**Plusieurs formats :**
* ini = plat
* yaml = plus homogène
* json = pour manipuler

Possiblité d'utiliser des patterns

Groupe racine => all

Groupes enfants

**Exemple :**
* un groupe parent1
	* groupes enfants : enfant1 et enfant2
		* "sous" enfant de enfant2 : enfant3

```
parent1 = srv4
		enfant1 = srv1 et srv2
		enfant2 = srv3
			enfant3= srv5
```

## Format **init**
```init
[parent1]
srv4
[enfant1]
srv1
srv2
[enfant2]
srv3
[enfant3]
srv5
[parent1:children]
groupe1
groupe2
[enfant2:children]
enfant3
```

## Format **yaml**
```yaml
all:
  children:
    parent1:
      hosts:
        srv4:
      children:
        enfant1:
          hosts:
            srv1:
            srv2:
        enfant2:
          hosts:
            srv3:
        children:
          enfant3:
            hosts:
              srv5:
```

Passer un groupe à un autre groupe : 
```yaml
all: 
  children:
    parent1:
      parent2:
      hosts:
        srv4:
      children:
        enfant1:
          hosts:
            srv1:
            srv2:
        enfant2:
          hosts:
            srv3:
          children:
            enfant3:
              hosts:
                srv5:
    parent2:
      hosts:
        srv6:
        srv7:
        srv8:
        srv9:
```

Utilisation de pattern : 
```yaml
all: 
  children:
    parent1:
      parent2:
      hosts:
        srv4:
      children:
        enfant1:
          hosts:
            srv[1:2]:
        enfant2:
          hosts:
            srv3:
          children:
            enfant3:
              hosts:
                srv5:
    parent2:
      hosts:
        srv[6:]:
```

Un peu plus vers la pratique
* couche commune > common
* serveurs web nginx > webserver
* bases de données > dbserver
* applications dockerisées ou non > app / appdock

Monitoring qui semble lié à toutes les machines users > monitoring

```yaml
all:
  children:
    common:
      children:
        webserver:
          hosts:
            srv[1:4]:
        dbserver:
          hosts:
            srv[5:6]:
        app:
          hosts:
            srv[7:10]:
        appdock:
          hosts:
            srv[11:15]:
    monitoring:
      children:
        common:
```

## Format Json
https://linuxhint.com/ansible_inventory_json_format/

# Les variables d'inventaires
<div class="video-responsive">
<iframe width="560" height="315" src="https://www.youtube.com/embed/UuiRDRIJ-sM" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
  </div>

## Informations
ansible = forte précédence des variables (ordre hiérarchique)

Regroupement par famille :
- Configuration settings
- Command-line options 		> VARIABLES D'INVENTAIRES
- Playbook keywords
- Variables

22 types:
```bash
        command line values (eg “-u user”)
        role defaults [1]
        inventory file or script group vars [2]
        inventory group_vars/all [3]
        playbook group_vars/all [3]
        inventory group_vars/* [3]
        playbook group_vars/* [3]
        inventory file or script host vars [2]
        inventory host_vars/* [3]
        playbook host_vars/* [3]
        host facts / cached set_facts [4]
        play vars
        play vars_prompt
        play vars_files
        role vars (defined in role/vars/main.yml)
        block vars (only for tasks in block)
        task vars (only for the task)
        include_vars
        set_facts / registered vars
        role (and include_role) params
        include params
        extra vars (always win precedence)
```
Doc : https://docs.ansible.com/ansible/latest/user_guide/playbooks_variables.html

## Variables d'inventaires
- fichier d'inventaire
- group_vars (répertoire)
- host_vars (répertoire)


## Squelette
```bash
├── 00_inventory.yml
├── group_vars
│   ├── all.yml
│   ├── dbserver.yml
│   └── webserver
│       ├── vault.yml
│       └── webserver.yml
└── host_vars
    ├── srv1
    │   └── srv1.yml
    └── srv2.yml
```

## Multienv
multienv >> c'est facile !!!
```bash
├── dev
│   ├── 00_inventory.yml
│   ├── group_vars
│   │   ├── all.yml
│   │   ├── dbserver.yml
│   │   └── webserver
│   │       ├── vault.yml
│   │       └── webserver.yml
│   └── host_vars
│       ├── srv1
│       │   └── srv1.yml
│       └── srv2.yml
├── prod
│   ├── 00_inventory.yml
│   ├── group_vars
│   │   ├── all.yml
│   │   ├── dbserver.yml
│   │   └── webserver
│   │       ├── vault.yml
│   │       └── webserver.yml
│   └── host_vars
│       ├── srv1
│       │   └── srv1.yml
│       └── srv2.yml
└── stage
    ├── 00_inventory.yml
    ├── group_vars
    │   ├── all.yml
    │   ├── dbserver.yml
    │   └── webserver
    │       ├── vault.yml
    │       └── webserver.yml
    └── host_vars
        ├── srv1
        │   └── srv1.yml
        └── srv2.yml
```

## Commande de test
```bash
ansible -i "node2," all -b -e "var1=xavki" -m debug -a 'msg={{ var1 }}'
```

## Exemples
Inventory :
```yaml
all:
  children:
    common:
      children:
        webserver:
          hosts:
            node[2:3]:
          vars:
            var1: "webserver"
        dbserver:
          hosts:
            node4:
            node5:
              var1: "node5"
          vars:
            var1: "dbserver"
    monitoring:
      children:
        webserver:
        dbserver:
```

Autour des group_vars
```
├── group_vars
│   ├── dbserver.yml
│   └── webserver.yml
└── inventory.yml
```
Ou encore
```
├── group_vars
│   ├── dbserver
│   │   └── dbserver.yml
│   └── webserver
│       └── webserver.yml
└── inventory.yml
```

Autour des host_vars
```
├── group_vars
│   ├── dbserver
│   │   └── dbserver.yml
│   └── webserver
│       └── webserver.yml
├── host_vars
│   ├── node2
│   │   └── variables.yml
│   └── node5.yml
└── inventory.yml
```

Et si on ajoutait un host au hasard et appartenant à "all"
```yaml
all:
  children:
    common:
      children:
        webserver:
          hosts:
            node[2:3]:
        dbserver:
          hosts:
            node4:
            node5:
    monitoring:
      hosts:
        node6:
      children:
        webserver:
        dbserver:
```
```
├── group_vars
│   ├── all.yml
│   ├── dbserver
│   │   └── dbserver.yml
│   └── webserver
│       └── webserver.yml
├── host_vars
│   ├── node2
│   │   └── variables.yml
│   └── node5.yml
└── inventory.yml
```

# La commande et le graph
<div class="video-responsive">
<iframe width="560" height="315" src="https://www.youtube.com/embed/D6Ru-M04G4M" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
  </div>
  
## Commande ansible-inventory
* pour y voir plus clair
* pour grapher



Export au format json par défaut (serveurs et variables d'inventaire)
```bash
ansible-inventory -i <inventory_file> --list
ansible-inventory -i <inventory_file> --yaml
```

Plus compact
```bash
ansible-inventory -i 00_inventory.yml --list --export
```

Afficher un format compact sans variables
```
ansible-inventory -i <inventory_file> --graph
```
Avec les variables
```
ansible-inventory -i 00_inventory.yml --graph --vars
```
Export vers un fichier
```
ansible-inventory -i <inventory_file> --output
```

Format toml
```
pip3 install toml
ansible-inventory -i <inventory_file> --vars --toml
```

## Mettre sous forme de graphique
```
pip3 install ansible-inventory-grapher
sudo apt install graphviz graphicsmagick-imagemagick-compat
ansible-inventory-grapher -i inventory.yml all | dot -Tpng | display png:-
```