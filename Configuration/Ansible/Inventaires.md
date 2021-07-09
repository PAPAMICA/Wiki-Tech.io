---
title: Ansible - Les inventaires
description: Créer et gérer les inventaires Ansible
published: true
date: 2021-07-09T08:01:35.009Z
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