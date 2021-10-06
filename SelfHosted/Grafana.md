---
title: SelfHosted - Grafana
description: Affichez proprement et facilement vos datas !
published: true
date: 2021-10-06T09:20:04.363Z
tags: supervision, grafana, monitoring, selfhosted
editor: markdown
dateCreated: 2021-10-06T09:12:40.045Z
---

![gafana-logo](https://labo-tech.fr/wp-content/uploads/2019/11/logo-grafana.png =500x)
# Présentation
Portée par l'américain Grafana Labs, Grafana est une plateforme open source taillée pour la surveillance, l'analyse et la visualisation des métriques IT. Elle est livrée avec un serveur web (écrit en Go) permettant d'y accéder via une API HTTP. Sous licence Apache 2.02, Grafana génère ses graphiques et tableaux de bord à partir de bases de données de séries temporelles (time series database) telles que Graphite, InfluxDB ou OpenTSDB. 

# Installation
## Debian
### Installez les sources Grafana
```bash
sudo apt-get install -y apt-transport-https
sudo apt-get install -y software-properties-common wget gnupg
wget -q -O - https://packages.grafana.com/gpg.key | sudo apt-key add -
```
Ajoutez ce dépôt pour les versions stables :
```bash
echo "deb https://packages.grafana.com/oss/deb stable main" | sudo tee -a /etc/apt/sources.list.d/grafana.list
```
Ajoutez ce dépôt si vous voulez des versions bêta :
```bash
echo "deb https://packages.grafana.com/oss/deb beta main" | sudo tee -a /etc/apt/sources.list.d/grafana.list
```
### Installer le paquet Grafana
```bash
sudo apt-get update
sudo apt-get install grafana
```

### Configurez votre Grafana
Toute la configuration de du serveur grafana se trouve dans le fichier `/etc/grafana/grafana.ini`
```bash
sudo nano /etc/grafana/grafana.ini
```

### Activez et lancez le service
```bash
sudo systemctl enable grafana-server
sudo systemctl start grafana-server
```

> Félicitation ! Votre serveur Grafana est accessible depuis `http://<ip_server>:3000`
{.is-success}

> Les identifiants par défaut sont : `admin` / `admin`
{.is-info}


## Docker
Vous pouvez déployer rapidement et simplement un serveur Grafana avec ce docker-compose :
```yaml
version: '2'
services:
  grafana:
    image: grafana/grafana:latest
    restart: always
    container_name: grafana
    volumes: 
      - /apps/grafana/config:/etc/grafana
      - /apps/grafana/data:/var/lib/grafana
    environment:
      - UPGRADEALL=true
      - GF_SECURITY_ALLOW_EMBEDDING=true
      - GF_PANELS_DISABLE_SANITIZE_HTML=true
      # Plugins to install
      - GF_INSTALL_PLUGINS=grafana-piechart-panel,grafana-worldmap-panel,snuids-trafficlights-panel,grafana-singlestat-panel
      - GF_PLUGINS_ALLOW_LOADING_UNSIGNED_PLUGINS=snuids-trafficlights-panel
      - GF_SERVER_DOMAIN=$URL
      - GF_SERVER_ROOT_URL=https://$URL
     
    # Facultatif
    networks:
      - proxy
#    ports:
#      - 3000:3000
    logging:
      driver: loki
      options:
        loki-url: "$URL_LOKI"
        loki-external-labels: service={{.Name}}
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.grafana.entrypoints=http"
      - "traefik.http.routers.grafana.rule=Host(`$URL`)"
      - "traefik.http.middlewares.grafana-https-redirect.redirectscheme.scheme=https"
      - "traefik.http.routers.grafana.middlewares=grafana-https-redirect"
      - "traefik.http.routers.grafana-secure.entrypoints=https"
      - "traefik.http.routers.grafana-secure.rule=Host(`$URL`)"
      - "traefik.http.routers.grafana-secure.tls=true"
      - "traefik.http.routers.grafana-secure.tls.certresolver=http"
      - "traefik.http.services.grafana-secure.loadbalancer.server.port=3000"
      - "traefik.docker.network=proxy"
      
networks:
  proxy:
    external:
      name: proxy
```
> Pensez à changer dans le docker-compose ou à définir les variables suivantes : **URL_LOKI** et **URL** en fonction de votre installation.
{.is-warning}

> Parfois le container ne se démarre pas car il n'arrive pas à créer le fichier `grafana.ini`, pensez à vérifier vos droits.
{.is-danger}


> Votre serveur Grafana est accessible via l'URL que vous avez configuré dans le docker-compose !
{.is-success}

> L'identifiant par défaut est `admin` et le mot de passe est `admin`.
{.is-info}


# Configuration
## Connexion à Zabbix
Pour connecter votre Grafana à votre serveur Zabbix, vous devez installer le plugin Zabbix d'Alexandre Zobnin.
### Debian
Connectez vous sur le serveur et utilisez `grafana-cli` pour installer le plugin :
```bash
sudo grafana-cli plugins install alexanderzobnin-zabbix-app
sudo systemctl restart grafana-server
```

### Docker
Pour Docker, ajoutez simplement le plugin à la liste des plugins à installer dans les variables d'environnement :
```yaml
			- GF_INSTALL_PLUGINS=alexanderzobnin-zabbix-app
			- GF_PLUGINS_ALLOW_LOADING_UNSIGNED_PLUGINS=alexanderzobnin-zabbix-datasource
```
### Configuration

