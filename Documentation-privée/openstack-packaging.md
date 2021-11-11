---
title: Openstack - Packaging
description: Mettre à jour et uploader les paquets OpenStack dans Debian.
published: true
date: 2021-08-24T12:24:26.099Z
tags: 
editor: markdown
dateCreated: 2021-08-24T07:45:17.852Z
---

# Télécharger le paquet
Se rendre dans le dossier `salsa-scripts` :
```bash
cd ~/stuff/salsa-scripts/
```

Se rendre dans le dossier du paquet :
```bash
cd <paquet>/<paquet>
```

Copier les fichiers `test-requirements.txt` et `requirements.txt` :
```bash
cp test-requirements.txt ../ && cp requirements.txt ../
```

Télécharger les mises du dode upstream :
```bash
./debian/rules fetch-upstream-remote
```

Repérez le dernier tag du paquets (`git tag`) et créez la variable `$TAG`
```bash
export TAG=<tag>
```

Vérifier que le paquet à été mis à jours pour la nouvelle version d'OpenStack (`meta:series: xena`)
```bash
git show $TAG
```

Faire un merge avec le dernier tag :
```bash
git merge -X theirs $TAG
```

Mettre à jour le changelog de Debian et faire un commit :
```bash
dch --newversion $TAG-1 -D experimental -m "New upstream release."
git commit -a -m "Now packaging $TAG (xena)"
```

Mettre à jours les fichiers `copyrigt` et `control`, `changelog` et faire un commit :
```bash
nano debian/copyright >>> (c) 2021 Mickael Asseline <mickael@papamica.com>
nano debian/control >>> Mickael Asseline <mickael@papamica.com>,
dch -r >>>   * Added myself in copyright and uploaders.
git commit -a -m "Added myself in copyright and uploaders."
```

Vérifier s'il y a eu des changements dans les dépendances :
```bash
diff -u ../test-requirements.txt test-requirements.txt
diff -u ../requirements.txt requirements.txt
```
> S'il n'y a aucun changement, passer à [Constuire le paquet](#constuire-le-paquet)
{.is-info}

# Mettre à jour le paquet 

Vérifier si la version existe avec :
```bash
rmadison <paquet>
```

Ajouter dans nano `debian/control` >>> après `Build-Depends-Indep:` :
```bash
nano debian/control
```
Faire le tri alphabétique dans les fichiers :
```bash
wrap-and-sort -bastk
```

Mettre à jour le `changelog` et faire un commit : 
```bash
dch -r >>>   * Added <paquet> to build-depends.
git commit -a -m "Added <paquet> to build-depends."
```

# Mettre à jour les fichiers avec upstream
Si vous rencontrez l'erreur suivante :
> **error**: aborting due to unexpected upstream changes
{.is-danger}

Récupérer le nom du fichier dans le log info juste au dessus :
> **info**: local changes detected, the modified files are: `python-sushy-3.11.1/tox.ini`
{.is-info}

Utiliser `mc` (`sudo apt install mc`) dans le dossier parent pour copier le fichier en question dans votre branche :
```bash
cd ..
mc
```
Dans la fenêtre de gauche, rentrez dans l'archive du paquet et allez chercher le fichier en question, dans la fenêtre de droite, rendez vous dans le dossier de votre paquet à l'emplacement du fichier. Pour le copier, cliquez sur `F5`.


# Constuire le paquet
```bash
./debian/rules gen-orig-xz
gbp buildpackage
```

Uploader le paquet :
```bash
git push
git push --tags
```

Construire le paquet avec Jenkins :
https://bullseye-xena.debian.net >>> paquet >>> build
  

