---
title: Proxy Zabbix
description: 
published: true
date: 2021-06-14T08:11:48.373Z
tags: 
editor: markdown
dateCreated: 2021-05-24T10:37:26.969Z
---

## **Installation**

Pour l’installation, là aussi, vous avez deux solutions : l’utilisation d’une VM chez votre client ou l’utilisation d’un mini pc ([un Raspberry Pi est parfait](https://amzn.to/36QW7EM)). Je vous recommande l’utilisation d’un Raspberry Pi, en plus de ne pas être trop cher, vous ne serez pas dépendant d’un serveur hyperviseur.

Pour l’installation, prévoyez un Debian 9 ou 10 en 64Bits ou Raspbian.

Installation de Git

```bash
apt install -y git
```

Récupération des scripts

```bash
git clone https://github.com/PAPAMICA/scripts
```

Exécuter le script

```bash
cd scripts/debian/zabbix-proxy
chmod +x debian_install_zabbix_proxy.sh
./debian_install_zabbix_proxy.sh
```

Il faut alors rentrer le nom de ce Proxy (**attention, cela devra être le même que dans votre serveur Zabbix**) ainsi que l’ip ou le ddns de votre serveur Zabbix.

## **Quelques commandes utiles**

Vu le nombre de personnes qui m’ont contacté suite aux précédents articles pour des commandes simples, voici celles que l’on m’a le plus demandées :

-   Afficher les containers Docker en cours : `*docker container ls*`
-   Arrêter les containers créés avec le scripts : (dans le dossier du script) `*docker-compose stop*`
-   Lancer les containers créés avec le scripts : (dans le dossier du script) `*docker-compose up -d*`
-   Afficher les logs du container : `*docker logs <id_container>*`
-   Entrer dans le container : *d*`*ocker exec -it <id_container> bash*`

Pour le reste des commandes, je vous invite à vous référer à mon article sur Labo-Tech : [Quelles sont les commandes de base de Docker ?](https://labo-tech.fr/base-de-connaissance/quelles-sont-les-commandes-de-base-de-docker/)

## **Configuration dans Zabbix**

Maintenant que notre Proxy est installé sur le réseau que nous souhaitons superviser, nous devons dire à Zabbix d’accepter les informations venant de ce Proxy.

Pour cela, connectez-vous sur votre Zabbix et rendez-vous dans la catégorie **Administration** puis **Proxys** :

![](https://www.tech2tech.fr/wp-content/uploads/2020/01/pro1-1024x117.png)

Cliquez sur « Créer un proxy » pour lancer la configuration de ce dernier.

Remplissez les champs suivants avec vos informations :

![](https://www.tech2tech.fr/wp-content/uploads/2020/01/pro2-1024x512.png)

Si vous le souhaitez, vous pouvez ajouter du chiffrement entre le Proxy et le serveur Zabbix, pour cela je vous invite à consulter la documentation Zabbix.

Votre Proxy est maintenant configuré, abordons maintenant la configuration d’une supervision à travers ce Proxy.

## **Supervision d’un équipement à travers le Proxy**

Pour cela, je vous invite à reprendre l’article précédent : [***Monitoring #3 : Surveillance de notre premier serveur***](https://www.tech2tech.fr/monitoring-3-surveillance-de-notre-premier-serveur/)

À l’étape 4, sélectionnez le proxy que vous utilisez pour cet équipement :

![](https://www.tech2tech.fr/wp-content/uploads/2019/12/O26WNRFVvF-1024x590.png)

![](https://www.tech2tech.fr/wp-content/uploads/2020/01/pro3-1024x775.png)

Votre équipement est maintenant supervisé via votre nouveau Proxy.