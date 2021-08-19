---
title: Certificat Let's Encrypt sur ESXI 7
description: Importer un certificat Let's Encrypt pour ESXI 7
published: true
date: 2021-06-14T08:15:46.637Z
tags: 
editor: markdown
dateCreated: 2021-05-24T10:37:35.560Z
---

J'ai en ma possession un certificat Wildcard Let's Encrypt qui certifie *.home.khroners.fr,* .khroners.fr et khroners.fr, au format .PEM.

# Conversion du certificat

Il est nécessaire de convertir le certificat au bon format.

Pour cela, nous allons utiliser Openssl sur une machine Linux.

```bash
openssl pkey -in privkey.pem -out privkey.key
openssl crl2pkcs7 -nocrl -certfile fullchain.pem | openssl pkcs7 -print_certs -out fullchain.crt
```

Ensuite, on récupère les fichiers fullchain.crt ainsi que privkey.key.

# Activation de la console SSH

Via l'interface Web, on clique sur “hôte” puis “Services” et “Activer Secure Shell (SSH)”.

![](/images/activer-ssh-esxi-7.png)

On se connecte ensuite en SSH.

# Importation du certificat

On fait une sauvegarde de la clé ainsi que du certificat actuel de l'ESXI.

```bash
cp rui.crt old.rui.crt
cp rui.key old.rui.key
```

On modifie rui.crt (avec vim par exemple), on efface son contenu et on colle le contenu du fichier fullchain.crt.

On fait de même pour rui.key avec le contenu de privkey.key.

On redémarre ensuite le serveur avec la commande reboot.

```bash
reboot
```

Sur l’interface web, on peut voir le certificat dans « Gérer », « Sécurité et utilisateurs » et « Certificats ».

![](/images/esxi-certificat.png)