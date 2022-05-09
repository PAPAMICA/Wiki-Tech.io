---
title: container-updater
description: Superviser et automatiser les mises à jour de container ! 
published: true
date: 2022-05-09T07:44:53.384Z
tags: update, docker
editor: markdown
dateCreated: 2022-05-09T07:44:53.384Z
---

# container-updater
🚀 Un petit script bash pour alerter et mettre à jour automatiquement les conteneurs déployés avec docker run, docker-compose ou Portainer.

🟣 Envoyer une notification à Discord (optionnel)

🔴 Envoyer des données à Zabbix (optionnel)

🔆 Exemple de notification Discord :
![ohunebellenotif](https://send.papamica.fr/f.php?h=25rsdWHk&p=1)

## Prérequis
```
jq, zabbix-sender (si vous utilisez Zabbix)
```

## Utiliser 
```bash
git clone https://github.com/PAPAMICA/container-updater
cd container-updater
./container-updater.sh
```

Vous pouvez envoyer une notification à Discord avec cet argument :
```bash
-d <discord_webhook>
```

Vous pouvez envoyer des données à Zabbix avec cet argument :
```bash
-z <serveur_zabbix>
-n <nom_hôte> (facultatif)
```
### Pour une exécution quotidienne, ajoutez un cron
```bash
00 09 * * * /chemin/vers/container-updater.sh -d <discord_webhook> -z <zabbix_server> >> /var/log/container-updater.log
```

## Supervision
Pour superviser les mises à jour d'un conteneur, il suffit d'ajouter ce label :
```yaml
labels:
    - "autoupdate=monitor"
```
Dans ce cas, si une mise à jour est disponible, le script enverra simplement une notification à Discord.
Tout ce que vous avez à faire est de mettre à jour le conteneur.

## Mise à jour automatique
Pour activer la mise à jour automatique du conteneur, vous devez ajouter ces labels :


### docker run
```bash
-l "autoupdate=true" -l "autoupdate.docker-run=true"
```

### docker-compose
```yaml
labels:
    - "autoupdate=true"
    - "autoupdate.docker-compose=/link/to/docker-compose.yml"
```

### Portainer
Vous devez avoir Portainer en version entreprise (licence gratuite jusqu'à 5 noeuds). 
Vous pouvez trouver le webhook dans les paramètres de la stack ou du conteneur.
```yaml
labels:
    - "autoupdate=true"
    - "autoupdate.webhook=<webhook_url>"
```

## A faire
- Ajouter le support des registres privés
- Meilleure génération de json pour les notifications Discord
- Support d'autres notifications


