---
title: Loki
description: 
published: true
date: 2021-05-28T11:28:26.678Z
tags: 
editor: markdown
dateCreated: 2021-05-24T10:35:08.382Z
---

![Loki Documentation | Grafana Labs](https://grafana.com/docs/loki/latest/logo_and_name.png)

# Présentation

Non, nous n'allons pas parler d'un personnage de l'univers Marvel ici mais d'un outil très pratique pour les admin sys !

[Loki](https://grafana.com/oss/loki/) est un agrégateur de logs créé et maintenu par l'équipe derrière [Grafana](https://grafana.com/grafana/). C'est une alternative plus simple à la [stack ELK](https://www.elastic.co/fr/what-is/elk-stack) pour la centralisation et la gestion des logs.

# Fonctionnement

Loki se compose de trois composants **Promtail**, **Loki** et **Grafana** que nous verrons brièvement avant de procéder au déploiement.

## Promtail

Promtail est un agent qui expédie le contenu des journaux locaux vers une instance Loki privée ou Grafana Cloud. Il est généralement déployé sur chaque machine sur laquelle des applications doivent être surveillées.

Il agit principalement:

-   Découvre des cibles
-   Attache des étiquettes aux flux de journaux
-   Les pousse vers l'instance Loki.

Actuellement, Promtail peut suivre les journaux de deux sources: les fichiers journaux locaux et le journal systemd (sur les machines AMD64 uniquement). [source](https://grafana.com/docs/loki/latest/clients/promtail/)

## Loki

Tel que cité par les créateurs de Loki, Loki est un système d'agrégation de journaux scalable horizontalement, hautement disponible et inspiré de Prometheus. Loki utilise le même mécanisme de découverte de service que celui de Prometheus et ajoute des étiquettes au flux de journal au lieu de l'indexation. Pour cette raison, les journaux reçus de Promtail se composent du même ensemble d'étiquettes que celui des métriques d'application. Ainsi, il offre non seulement une meilleure commutation de contexte entre les journaux et les métriques, mais évite également la journalisation complète des index.

## Grafana

Grafana est une plate-forme open-source spécialisé dans l'analyse et la surveillance. Il fonctionne spécifiquement sur des données chronologiques provenant de sources telles que Prometheus et Loki. De plus, il vous permet d'interroger, de visualiser, d'alerter sur les métriques quel que soit son emplacement stocké. Il aide à créer, explorer et partager des tableaux de bord et encourage la culture axée sur les données.

# Installation

Evidemment, si vous ne l'aviez pas encore deviné, nous allons déployer Loki dans un conteneur Docker avec Docker-Compose !

Voici le docker-compose.yml en question : 

```yaml
version: "2"

networks:
  proxy:
    external:
      name: proxy

services:
  loki:
    image: grafana/loki:1.5.0
    container_name: loki
    restart: always
    ports:
      - "3100:3100"
    command: -config.file=/etc/loki/local-config.yaml
    networks:
      - proxy

  promtail:
    image: grafana/promtail:1.5.0
    container_name: loki-promtail
    restart: always
    volumes:
      - /var/log:/var/log
    command: -config.file=/etc/promtail/docker-config.yaml
    networks:
      - proxy
```

Loki et son compagnon Promtail sont maintenant en fonctionnement !   
Le lien pour envoyer vos logs est le suivant : **http://server:3100/loki/api/v1/push**

# Envoyer les logs

## Depuis Docker

Il est très simple d'envoyer les logs Docker directement à Loki : un plugin est disponible pour cela.

### Installer le plugin

```bash
docker plugin install grafana/loki-docker-driver:latest --alias loki --grant-all-permissions
```

Pour vérifier l'installation du plugin, vous pouvez utiliser la commande `docker plugin ls` :

```bash
docker plugin ls
ID                  NAME         DESCRIPTION           ENABLED
ac720b8fcfdb        loki         Loki Logging Driver   true
```

### Mettre à jour le plugin

```bash
docker plugin disable loki --force
docker plugin upgrade loki grafana/loki-docker-driver:latest --grant-all-permissions
docker plugin enable loki
systemctl restart docker
```

### Configurer le plugin

Plusieurs options existent : 

1.  A chaque lancement de container avec `docker run` :

```yaml
docker run --log-driver=loki \
    --log-opt loki-url="<URL_LOKI>" \
    --log-opt loki-retries=5 \
    --log-opt loki-batch-size=400 \
    grafana/grafana
```

 2. En ajoutant dans vos docker-compose :

```yaml
    logging:
      driver: loki
      options:
        loki-url: "<URL_LOKI>"
        loki-external-labels: service={{.Name}}
```

 3. En configurant le driver comme driver par défaut dans Docker :

Pour cela vous devez changer le fichier `daemon.json` de Docker (dans `/etc/docker` sur Linux) en ajoutant ceci : 

```json
{
    "debug" : true,
    "log-driver": "loki",
    "log-opts": {
        "loki-url": "<URL_LOKI>",
        "loki-batch-size": "400"
    }
}
```

Après avoir configurer édité le fichier daemon.json, veuillez relancer le service docker avec :

```bash
sudo systemctl restart docker 
```

Et voilà, tous les **prochains** container créés enverrons leurs logs à Loki !

## Depuis Debian

Depuis Debian ou tout autres distributions Linux c'est un peu plus compliqué : il faut installer et configurer l'agent Promtail pour l'envoi des logs.

 1. Téléchargez le binaire de Promtail depuis le [Github](https://github.com/grafana/loki/releases/) :

```bash
curl -s https://api.github.com/repos/grafana/loki/releases/latest | grep browser_download_url |  cut -d '"' -f 4 | grep promtail-linux-amd64.zip | wget -i -
```

 Une fois le téléchargement terminé, dézippez le dans `/usr/local/bin` :

```bash
unzip promtail-linux-amd64.zip
sudo mv promtail-linux-amd64 /usr/local/bin/promtail
```

 2. Créez un fichier de configuration YAML pour Promtail dans le dossier `/usr/local/bin` :

```bash
sudo nano /etc/promtail-local-config.yaml
```

 3. Ajoutez le contenu suivant au fichier :

```yaml
server:
  http_listen_port: 9080
  grpc_listen_port: 0

positions:
  filename: /data/loki/positions.yaml

clients:
  - url: <URL_LOKI>

scrape_configs:
- job_name: system
  static_configs:
  - targets:
      - localhost
    labels:
      job: varlogs
      __path__: /var/log/*log
```

 4. Créez le service pour Promtail :

```bash
sudo tee /etc/systemd/system/promtail.service<<EOF
[Unit]
Description=Promtail service
After=network.target

[Service]
Type=simple
User=root
ExecStart=/usr/local/bin/promtail -config.file /etc/promtail-local-config.yaml

[Install]
WantedBy=multi-user.target
EOF
```

 5. Lancez le service Promtail :

```plaintext
sudo systemctl daemon-reload
sudo systemctl start promtail.service
```

Vous pouvez vérifier le statut du service avec `systemctl status promtail.service`. L'agent Promtail est maintenant installé et configuré pour envoyer vos logs systemd vers Loki.

# Grafana

## Configuration de Loki

Pour voir les logs et leurs statistiques, il faut connecter Loki dans Grafana. 

Dans Grafana, ajouter la source de donnée Loki :

![](/images/image_2021-04-30_185059.png)

![](/images/loki2.png)

Et configurer la source de donnée de cette façon :

Dans URL, mettez l'URL de votre Loki `http://serveur:3100` ou `nom-du-container:3100` si vous utilisez Loki sous docker sur le même réseau que Grafana.

![](/images/loki3.png)

Vos logs sont maintenant accessible dans Grafana. Allons voir comment les consulter !

## Explorer les logs

Vous pouvez consulter vos logs brut directement depuis l'onglet Explorer de Grafana :

![](/images/image_2021-04-30_193550.png)

A vous de jouer avec les requêtes pour remonter les logs que vous souhaitez.

## En cadeau, des jolis tableaux

Vu que je suis particulièrement gentil, je vous partage mes deux petits bébés : mes dashboards Grafana pour mes logs.

### La vue serveurs :

![](/images/image_2021-04-30_195917.png)

Dashboard disponible ici : [Github](https://github.com/PAPAMICA/Templates/blob/master/Grafana/Loki-Logs-Serveurs.json)

### La vue services :

![](/images/image_2021-04-30_194541.png)

Dashboard disponible ici : [Github](https://github.com/PAPAMICA/Templates/blob/master/Grafana/Loki-Logs-Services.json)