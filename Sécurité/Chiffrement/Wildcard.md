---
title: Certificat Wildcard - OVH
description: Génération d'un certificat Wildcard pour tous vos sous-domaines d'OVH
published: true
date: 2021-06-14T08:16:59.397Z
tags: 
editor: markdown
dateCreated: 2021-05-24T10:37:31.253Z
---

# Certificat Wildcard avec Certbot et OVH

## Présentation

La présence d'un certificat SSL sur vos sites web est essentiel pour la sécurisation des transferts de données. Celui-ci est nécessaire pour le bon fonctionnement du protocole HTTPS, remplaçant du protocole HTTP.

Le protocole HTTPS (HyperText Transfer Protocol Secure) permet de sécuriser les échanges entre le serveur et le visiteur. On parle alors de communication chiffrée. Pour cela, HTTPS utilise un protocole de transport appelée SSL/TLS.

Avec Let's Encrypt, il est possible de générer des certificats SSL pour un domaine ou sous-domaine, mais il est également possible de générer un certificat pour plusieurs sous-domaines. On appelle ces certificats des certificats Wildcard. Ils certifient des noms DNS du type \*.exemple.fr.

## Préparatifs

Dans mon cas, mon nom de domaine est enregistré sur OVH. Pour générer le Wildcard, nous allons utiliser un plugin de certbot, [dns-ovh](https://certbot-dns-ovh.readthedocs.io/en/stable/).

### Installation des dépendances

```bash
apt install python3-pip
apt install certbot
apt install python3-certbot-dns-ovh
```

### Création des accès à l'API d'OVH

On se rend sur [le site de l'API d'OVH](https://api.ovh.com/createToken/) pour créer nos clés. Voici les droits à attribuer :

-   `GET /domain/zone/`
-   `GET: /domain/zone/{domain}/`
-   `GET /domain/zone/{domain}/status`
-   `GET /domain/zone/{domain}/record`
-   `GET /domain/zone/{domain}/record/*`
-   `POST /domain/zone/{domain}/record`
-   `POST /domain/zone/{domain}/refresh`
-   `DELETE /domain/zone/{domain}/record/*`

On remplace {domain} par notre nom de domaine, sans les accolades. Par exemple : exemple.fr.

Accound ID correspond à votre Nichandle, présent dans l'interface de gestion de compte OVH, sous la forme xx11111-ovh.

On obtient ensuite une liste d'identifiants : Application Key, Application Secret, Consumer Key.

Sur notre serveur, on crée un fichier ".ovhapi". A titre d'exemple, je le place dans /root/. 

```bash
nano /root/.ovhapi
```

On rentre les identifiants sous cette forme :

```bash
dns_ovh_endpoint = ovh-eu
dns_ovh_application_key = xxx
dns_ovh_application_secret = xxx
dns_ovh_consumer_key = xxx
```

On attribue ensuite les droits sur ce fichier.

```bash
chmod 600 /root/.ovhapi
```

## Génération du certificat Wildcard

La génération du certificat s'effectue en une seule ligne de commande :

```bash
certbot certonly --dns-ovh --dns-ovh-credentials ~/.ovhapi -d exemple.fr -d *.exemple.fr
```

Pensez bien à remplacer “exemple.fr” par votre nom de domaine.

Dans ce cas, le domaine exemple.fr ainsi que tous ses sous-domaines seront certifiés. Voici le résultat :

![](/images/certificat_wildcard.png)

[Source d'origine](https://docs.khroners.fr/books/debian/page/generer-un-certificat-wildcard) | *Rédacteur Alexis BONNET*