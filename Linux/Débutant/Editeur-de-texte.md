---
title: Linux - Les éditeurs de textes
description: Comprendre et utiliser les éditeurs de textes comme VIM, Nano, Joe, etc..
published: true
date: 2021-08-03T06:40:24.084Z
tags: 
editor: markdown
dateCreated: 2021-05-24T10:35:25.944Z
---

![Vim — Wikipédia](https://upload.wikimedia.org/wikipedia/commons/thumb/9/9f/Vimlogo.svg/1200px-Vimlogo.svg.png)

# VIM

## Présentation

Vim est un éditeur de texte, c’est-à-dire un logiciel permettant la manipulation de fichiers texte. Il est directement inspiré de vi, dont il est le clone le plus populaire. Son nom signifie d’ailleurs Vi IMproved, que l’on peut traduire par « VI aMélioré »

## Manipulation de base

| Commande | Commentaire |
| --- | --- |
| `:w` | Sauvegarder le fichier |
| `:x ou :wq` | Enregistrer et quitter |
| `:q` | Quitter si aucun changement n'a été effectué |
| `:q!` | Quitter et annuler les changements |
| `:set nu` | Afficher le numéro des lignes |
| `:r *fichier*` | Importer un *fichier* dans le fichier actuel |
| `:**n** r *fichier*` | Importer un *fichier* dans le fichier actuel à partir de la ligne **n** |

## Suppression

| Commande | Commentaire |
| --- | --- |
| `x` | Supprimer un caractère unique (celui sur lequel est positionné le curseur |
| `X` | Supprimer le caractère derrière le curseur |
| `D` | Supprimer le reste de la ligne à partir du curseur |
| `dd` | Supprimer une ligne entière |
| `**n**dw` | Supprimer les prochains **n** mots |
| `**n**dd` | Supprimer les **n** prochaines lignes |
| `:**x**,**y**d` | Supprimer depuis la ligne **x** à la ligne **y** |

## Entrer en mode insertion (modification)

| Commande | Commentaire |
| --- | --- |
| `i` | Insérer sur le curseur |
| `a` | Insérer après le curseur |
| `I` | Insérer sur la ligne suivante |
| `A` | Insérer sur la ligne précédente |
| `o` | Insérer une nouvelle ligne après la ligne actuelle |
| `O` | Insérer une ligne avant la ligne actuelle |
| `**ESC**` | Quitter le mode d'insertion |

## Copier Coller

| Commande | Commentaire |
| --- | --- |
| `p` | Coller le contenu du presse papier en dessus de la ligne |
| `P` | Coller le contenu du presse papier au dessus de la ligne |
| `yy` | Copier une ligne |
| `yw` | Copier un mot |
| `y$` | Copier depuis la position du curseur jusqu'à la fin de la ligne |

## Rechercher et remplacer

| Commande | Commentaire |
| --- | --- |
| `/*mot souhaité*` | Rechercher le *mot souhaité* après la position du curseur |
| `?*mot souhaité*` | Rechercher le *mot souhaité* avant la position du curseur |
| `n` | Trouver la prochaine occurrence du *mot souhaité* |
| `N` | Trouver l'occurrence précédente |
| `:%s/*ancien mot*/*nouveau mot*/g` | Remplacer toutes les occurrences de l'*ancien mot* par le *nouveau mot* |

## Se déplacer

| Commande | Commentaire |
| --- | --- |
| `h` | Se déplacer de 1 caractère à gauche |
| `j` | Se déplacer de 1 caractère en bas |
| `k` | Se déplacer de 1 caractère en haut |
| `l` | Se déplacer de 1 caractère à droite |
| `w` | Se déplacer au mot suivant |
| `b` | Se déplacer au début du mot |
| `e` | Se déplacer à la fin du mot |
| `(` | Se déplacer au début d'une phrase |
| `)` | Se déplacer à la fin d'une phrase |
| `{` | Se déplacer au paragraphe précédent |
| `}` | Se déplacer au paragraphe suivant |
| `^` | Se déplacer au début de la ligne |
| `$` | Se déplacer à la fin de la ligne |
| `<**n**>G` | Se déplacer à la **n**ème line |
| `G` | Se déplacer à la dernière ligne |
| `gg` | Se déplacer à la première ligne |
| `%` | Se déplacer à la parenthèse correspondante |

---

![GNU nano — Wikipédia](https://upload.wikimedia.org/wikipedia/commons/thumb/8/8a/Gnu-nano.svg/1200px-Gnu-nano.svg.png)

# Nano

## Présentation

Ce petit éditeur est un clone libre de “pico” qui était inclus dans le logiciel de gestion de courrier “Pine”. Nano utilise la bibliothèque “ncurse” pour offrir une interface dite “semi-graphique” à l'intérieur d'une console. Ici pas de surenchère de fonctions, pas de raccourcis “à quatre mains”, juste ce qu'il faut pour éditer un fichier, y effectuer une recherche, c'est un outils simple et efficace pour le spécialiste, mais surtout c'est peut-être le seul éditeur de texte en console totalement accessible au grand débutant.

## Utilisation basique

| Commande | Commentaire |
| --- | --- |
| `CTRL + A` | Aller au début de la ligne. |
| `CTRL + E` | Aller à la fin de la ligne. |
| `CTRL + Y` | Faire défiler la page vers le bas. |
| `CTRL + V` | Faire défiler la page vers le haut. |
| `CTRL + G` | Cette commande ouvre une **fenêtre d’aide** qui contient des informations concernant toutes les commandes que vous pouvez utiliser avec l’éditeur Nano. |
| `CTRL + O` | Pour sauvegarder votre fichier. Lorsque cette commande est utilisée, vous serez invité à modifier ou vérifier le nom de fichier souhaité et après avoir appuyé sur Entrée, elle enregistrera votre fichier. |
| `CTRL + W` | Une des commandes les plus utiles. Elle est utilisée pour rechercher une expression spécifique dans votre texte. Elle fonctionne comme la commande **CTRL + F** sur d’autres applications. Pour rechercher la même expression, appuyez sur **ALT + W** autant de fois qu’il le faut. |
| `CTRL + K` | Coupe toute la ligne sélectionnée dans le “**presse-papier**”. |
| `CTRL + U` | Colle le texte du “presse-papier” dans la ligne sélectionnée. |
| `CTRL + J` | Justifie le paragraphe actuel. |
| `CTRL + C` | Affiche la position actuelle du curseur dans le texte (ligne / colonne / caractère). |
| `CTRL + X` | Quitte l’éditeur de texte Nano. Dans le cas où vous avez apporté des modifications au fichier, elle vous invite à sauvegarder. |
| `CTRL + R` | Ouvre un fichier avec la commande « Lire le fichier ». Insère le fichier depuis le disque/dossier à la position actuelle du curseur. |
| `CTRL + \` | Remplace les mots spécifiés. |
| `CTRL + T` | Met en route le correcteur orthographique si disponible. |
| `CTRL + _` | Va au numéro de ligne et de colonne spécifié. |
| `ALT + A` | Pour sélectionner un texte. Vous pouvez combiner cette commande avec **CTRL + K** pour couper une partie spécifique du texte. |

## Activer la coloration syntaxique
L'éditeur nano fournit lui-même la mise en évidence de la syntaxe pour quelques langages et scripts. Vous pouvez les retrouver dans `/usr/share/nano/` :
```bash
asm.nanorc     fortran.nanorc   man.nanorc     ocaml.nanorc   ruby.nanorc
awk.nanorc     gentoo.nanorc    mgp.nanorc     patch.nanorc   sh.nanorc
c.nanorc       groff.nanorc     mutt.nanorc    perl.nanorc    tcl.nanorc
cmake.nanorc   html.nanorc      nano-menu.xpm  php.nanorc     tex.nanorc
css.nanorc     java.nanorc      nanorc.nanorc  pov.nanorc     xml.nanorc
debian.nanorc  makefile.nanorc  objc.nanorc    python.nanorc
```
Afin d'avoir un peu de couleurs dans vos fichiers textes, vous pouvez activer la coloration syntaxique directement dans nano en ajouter cette ligne dans le fichier `~/.nanorc` :
```bash
include /usr/share/nano/sh.nanorc
```

---

# Joe

## Présentation

## Utilisation