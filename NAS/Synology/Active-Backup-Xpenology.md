---
title: Active Backup sur Xpenology
description: Profiter d'Active Backup sur XPEnology
published: true
date: 2021-06-14T08:00:36.988Z
tags: 
editor: markdown
dateCreated: 2021-05-24T10:36:21.383Z
---

![](https://i0.wp.com/labo-tech.fr/wp-content/uploads/2019/11/synology_activebackup.png?resize=617%2C338&ssl=1)

# Activer Active Backup sur Xpenology

Lorsque vous souhaitez utilisez Active Backup sur Xpenology, vous vous retrouverez bloqué par une activation avec un compte Synology. Heureusement, il existe une astuce pour contourner cette activation

## NAS avec certificat SSL :

Sur votre ordinateur connecté au même réseau que votre NAS, entrez ces deux URL dans votre navigateur en intégrant votre IP, votre login, votre mot de passe et votre serial :

```plaintext
https://IP_DE_MON_SYNO:5001/webapi/auth.cgi?api=SYNO.API.Auth&method=Login&version=1&account=MON_LOGIN&passwd=MON_MOT_DE_PASSE
https://IP_DE_MON_SYNO:5001/webapi/entry.cgi?api=SYNO.ActiveBackup.Activation&method=set&version=1&activated=true&serial_number=MON_SERIAL
```

## NAS sans certificat SSL :

Sur votre ordinateur connecté au même réseau que votre NAS, entrez ces deux URL dans votre navigateur en intégrant votre IP, votre login, votre mot de passe et votre serial :

```plaintext
http://IP_DE_MON_SYNO:5000/webapi/auth.cgi?api=SYNO.API.Auth&method=Login&version=1&account=MON_LOGIN&passwd=MON_MOT_DE_PASSE
http://IP_DE_MON_SYNO:5000/webapi/entry.cgi?api=SYNO.ActiveBackup.Activation&method=set&version=1&activated=true&serial_number=MON_SERIAL
```

Profitez pleinement de la solution Active Backup de Synology !