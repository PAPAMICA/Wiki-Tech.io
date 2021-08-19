---
title: Script Altaro-Zabbix
description: Remonter les informations Altaro dans Zabbix
published: true
date: 2021-07-09T14:02:30.190Z
tags: 
editor: markdown
dateCreated: 2021-05-24T10:37:24.847Z
---

![Altaro (AltaroSoftware) - Profile | Pinterest](https://i.pinimg.com/originals/3e/d3/d9/3ed3d9720cbcdfc72a32fec261627482.png)

# Présentation

Altaro est un logiciel de sauvegarde de VM pour Hyper-V et pour VMWare ESXi. Il fonctionne sous Windows et peux sauvegarder les disques des machines virtuelles directement en local ou à distance.

Notre but aujourd'hui est de remonter le statut des dernières sauvegardes dans notre serveur Zabbix.

Heureusement pour nous (et ça devrais être un standard), il inscrit ses actions et ses résultats dans l'observateur d’événement Windows. Nous allons donc récupérer ces logs, les utiliser pour en déduire le statut des dernières sauvegardes et les transmettre à Zabbix grâce à zabbix-sender.

Site officiel d'Altaro : [altaro.com](https://www.altaro.com)

# Installation

## Modèle Zabbix

Afin d'interpréter les données que nous allons envoyer à notre serveur Zabbix, nous devons importer un petit modèle qui va comprendre la donnée, l'afficher et générer automatiquement une alerte en cas d’échec de la sauvegarde ou si on ne récupère plus l'information depuis plus de 48h.

 1 - Téléchargez le modèle disponible sur mon [Github](https://github.com/PAPAMICA/Templates/tree/master/Zabbix-Altaro)

 2 - Connectez vous à votre serveur Zabbix

 3 - Rendez vous dans l'onglet “*Configuration*” et dans la catégorie “*Modèles*”

 4 - Cliquez sur le bouton “*Importer*” en haut à droite et choisissez le fichier “*Template App Altaro VM Backup.xml*”

 5 - Un jolie message devrais s'afficher pour valider l'importation :

![](/images/image_2021-05-03_120319.png)

## Script Powershell

Le script Powershell doit être téléchargé et exécuté sur le même serveur que la console Altaro (en général le serveur hôte Hyper-V) :

 1 - Téléchargez le script disponible sur mon [Github](https://github.com/PAPAMICA/Templates/tree/master/Zabbix-Altaro)

 2 - Copiez le dans le dossier de votre Agent Zabbix (en général : “*C:\\Program Files\\Zabbix Agent*”)

 3 - Testez le fonctionnement du script avec la commande :

```powershell
powershell.exe -NoProfile -NoLogo - File "C:\Program Files\Zabbix Agent\Get-AltaroBackupStatus.ps1" "<CLIENT - HOST>" [Onsite/Offsite] <ZABBIX_IP>
```

Avec : 

-   `"<CLIENT - HOST>"` = Le nom de l'hôte dans Zabbix entre guillemets
-   `[Onsite/Offsite]` = Le choix de la sauvegarde Onsite ou Offsite
-   `<ZABBIX_IP>` = L'IP de votre serveur Zabbix (pas de DDNS)

Par exemple :

```powershell
powershell.exe -NoProfile -NoLogo - File "C:\Program Files\Zabbix Agent\Get-AltaroBackupStatus.ps1" "CLIENT - HOST1>" Onsite 192.168.5.4
```

> Si vous utilisez un proxy Zabbix, il faut mettre l'IP de ce proxy !
{.is-warning}

> Pensez à modifier l'URL du dossier de l'Agent Zabbix dans le script s'il n'est pas dans “*C:\\Program Files\\Zabbix Agent*” (Ligne 36 : $ZabbixFolder = “<chemin>”)
{.is-warning}

## Créer une tache

Si vous souhaitez que le script s’exécute tous les jours automatiquement, le plus simple est de passer par une tâche automatique.

1.  Créez une tâche avec en déclencheur une exécution tous les jours
2.  En action : Démarrer un programme avec “powershell.exe” dans Programme et "`-NoProfile -NoLogo - File "C:\Program Files\Zabbix Agent\Get-AltaroBackupStatus.ps1" "<CLIENT - HOST>" [Onsite/Offsite] <ZABBIX_IP>`" dans les arguments.

![](/images/image_2021-05-03_121832.png)

> Vous pouvez créer 2 tâches si vous souhaitez superviser les sauvegardes Offsite et Onsite.
{.is-info}
