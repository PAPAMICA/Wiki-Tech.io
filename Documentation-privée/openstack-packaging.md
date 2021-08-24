---
title: Openstack - Packaging
description: Mettre à jour et uploader les paquets OpenStack dans Debian.
published: true
date: 2021-08-24T07:45:17.852Z
tags: 
editor: markdown
dateCreated: 2021-08-24T07:45:17.852Z
---

# Procédure
cd ~/stuff/salsa-scripts/paquet
cp test-requirements.txt ../ && cp requirements.txt ../
./debian/rules fetch-upstream-remote
git show <tag> >>> meta:series: xena
git merge -X theirs <tag>
dch --newversion <tag>-1 -D experimental -m "New upstream release."
git commit -a -m "Now packaging <tag> (xena)"
nano debian/copyright >>> (c) 2021 Mickael Asseline <mickael@papamica.com>
nano debian/control >>> Mickael Asseline <mickael@papamica.com>,
dch -r >>>   * Added myself in copyright and uploaders.
git commit -a -m "Added myself in copyright and uploaders."
diff -u ../test-requirements.txt test-requirements.txt
diff -u ../requirements.txt requirements.txt
./debian/rules gen-orig-xz
gbp buildpackage
git push
git push --tags
https://bullseye-xena.debian.net >>> paquet >>> build
  

# TEST REQUIREMENTS
Vérifier si la version existe avec rmadison <paquet>
Ajouter dans nano debian/control >>> après Build-Depends-Indep:
wrap-and-sort -bastk
dch -r >>>   * Added <paquet> to build-depends.
git commit -a -m "Added <paquet> to build-depends."