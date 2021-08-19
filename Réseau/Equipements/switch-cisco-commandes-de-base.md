---
title: Switch Cisco
description: Commandes de base
published: true
date: 2021-08-14T10:25:24.149Z
tags: commandes, cisco, switch
editor: markdown
dateCreated: 2021-08-14T10:09:16.927Z
---

# Administration

|     |     |
| --- | --- |
| `enable` | Passez du mode d’exécution utilisateur au mode d’exécution privilégié |
| `disable` | Passez du mode d’exécution privilégié au mode d’exécution utilisateur |
| `configure terminal` | Passez du mode d’exécution privilégié au mode de configuration globale |
| `interface fastethernet <n°> ` | Passez du mode d’exécution configuration globale au mode configuration d’interface |
| `exit` | Passez du mode de configuration d’interface en mode configuration globale |
| `show history` | Affiche l’historique des commandes |
| `show interfaces` | Affiche l’état et la configuration d’une interface disponible sur le commutateur |
| `show interface n°` | Affiche l’état et la configuration d’une interface disponible sur le commutateur |
| `show startup-config` | Affiche le contenu de la configuration de démarrage |
| `show running-config] ` | Affiche la configuration actuelle |
| `show flash` | Affiche des informations sur le système de fichiers flash |
| `show version` | Affiche l’état du logiciel et du matériel système |
| `show mac-address-table` | Affiche la table de transmission MAC |
| `ip {interface | http | arp}` | Affiche des informations IP. L’option d’interface dévoile l’état et la configuration de l’interface IP. L’option http affiche les données HTTP relatives au gestionnaire de périphériques exécuté sur le commutateur. L’option arp affiche la table ARP IP. |

# Configuration de la sécurité

> Être en mode de configuration globale
{.is-info}

### Mot de passe console

|     |     |
| --- | --- |
| `line con <n°console> ` | Passer en mode de configuration de ligne pour la console n° |
| `password <mot-de-passe> ` | Définir le mot de passe pour la console n° |
| `login` | Permet d’exiger la saisie du mot de passe avant d’avoir l’accès à la console |
| `end` | Quitter le mode de configuration de ligne et revenir en mode privilégié |

### Mot de passe privilégié

|     |     |
| --- | --- |
| `enable secret <mot_de_passe> ` | Définir le mot de passe pour l’exécution du mode privilégié |
| `end` | Quitter le mode de configuration de ligne et revenir en mode privilégié |

# Enregistrement de la configuration

|     |     |
| --- | --- |
| `copy running-config startup-config` | Permet d’enregistrer la configuration de la mémoire RAM sur la mémoire flash. |
| `copy startup-config running-config` | Permet d’importer la configuration de démarrage sur la mémoire RAM. |

# Configuration VLAN

> Être en mode de configuration globale
{.is-info}

### Création VLAN

|     |     |
| --- | --- |
| `vlan <id_vlan>` | Crée un VLAN, <id_vlan> est le numéro du VLAN à crée ou permet de passer en mode de configuration pour le VLAN <id_vlan> |
| `name <nom_vlan> ` | Permet de donner un nom au VLAN |

### Affectation de port à un VLAN

|     |     |
| --- | --- |
| `switchport access vlan <id_vlan>` | Affecte l’interface du commutateur au VLAN <id_vlan> |
| `no switchport access vlan <id_vlan>` | Supprime l’affectation de l’interface au VLAN <id_vlan> |

### Information sur les VLAN

|     |     |
| --- | --- |
| `show vlan` | Affiche tous les VLAN |
| `show vlan [id_vlan]` | Affiche des informations sur un VLAN identifié par son ID |
| `show vlan name [nom_vlan]` | Affiche des informations sur un VLAN identifié par son nom |

*Rédacteur Kevin ENGEL*