---
title: Linux - Terminal
description: Toutes les astuces pour votre terminal !
published: true
date: 2021-09-10T09:31:08.716Z
tags: 
editor: markdown
dateCreated: 2021-05-24T10:35:34.861Z
---

![How to beautify or enhance the appearance linux terminal - Ahan-Ganteng](https://2.bp.blogspot.com/-K6OX_DJBHl0/Vms2vUM7PII/AAAAAAAAA4M/QCNAHS_RLeY/s1600/Terminal-icon.png =20%x)

# Choix du terminal

## La base

Simplement celui de votre environnement de bureau !

Pour ma part j'utilise KDE qui fournit Konsole : simple, personnalisable et bien pensé. Il est un très bon terminal pour débuter sous Linux.

## Le jolie et complet

Mon petit chouchou, disponible sous **Linux**, **MacOS** et **Windows** : [Extraterm](https://extraterm.org/index.html)

Il est développé avec Electron ce qui le rend accessible sur tous les OS mais il souffre aussi de quelques ralentissement par moment. Cependant il intègre beaucoup de fonctionnalités très intéressantes comme :

-   La commande `show` qui vous permettra de **voir vos images dans votre terminal**
-   Un système d'onglets et de fenêtres complet
-   Un système de **recherche de commandes intégré**
-   L'isolation de la sortie des commandes dans des “frames”
-   Un gestionnaire de téléchargement
-   Récupération de la sortie d'une commande précédente
-   Le support des emoji nativement
-   L'accélération GPU

Et il est évidemment OpenSource, bref c'est vraiment un terminal à tester !

![Extraterm keyboard cursor and selection](https://extraterm.org/selection_mode2.gif)

## Le rapide

Si vous aimez la rapidité plus que tout (coucou Arnold), il serait temps de vous intéresser à ce petit terminal qui mise tout dessus : [Alacritty](https://github.com/alacritty/alacritty) !

Alacritty est un émulateur de terminal moderne qui est livré avec une configuration par défaut simple, mais permet une configuration étendue. 

En s'intégrant à d'autres applications, plutôt qu'en réimplémentant leurs fonctionnalités, il parvient à fournir un ensemble flexible de fonctionnalités à hautes performances. Les plates-formes prises en charge sont actuellement **BSD**, **Linux**, **macOS** et **Windows**.

Vous retrouverez la liste des fonctionnalités à jours ici : [features.md](https://github.com/alacritty/alacritty/blob/master/docs/features.md)

Ce terminal est considéré comme étant en bêta encore actuellement; il y a quelques fonctionnalités manquantes et quelques bugs à corriger, mais il est déjà utilisé par beaucoup comme terminal par défaut.

Les binaires précompilés sont disponibles sur [GitHub](https://github.com/alacritty/alacritty/releases).

## Le simple

Il porte bien son nom car il se nome : [st](https://st.suckless.org) pour “Simple Terminal" !

Un petit retour aux sources, pas de fonctionnalités “superflus” ici, juste votre terminal. Un outil parfait pour les puristes de Linux.

## Le discret

Intégré nativement dans KDE, il me semble nécessaire de le mentionner ici vu que je l'utilise au quotidien en supplément de mon terminal par défaut.

Je parle de [Yakuake](https://apps.kde.org/yakuake/) qui, d'une touche, fait apparaître un terminal du haut de votre écran et “se range” dès que vous avez terminé. Il est si accessible que c'est ce dernier que j'utilise lorsque j'ai juste une petite commande à faire. Il s'intègre parfaitement avec mon workflow !

# Zsh - Oh-My-Zsh

## Présentation

ZSH permet de remplacer aisément le shell (Bash) par défaut dans les distributions Linux.

Il permet de rendre plus agréable et plus interactif votre terminal sous Linux.

On peut noter ceci :  
 - Autocomplétion améliorée  
 - Personnalisation avancée (au niveau des couleurs par exemples)  
 - Possibilités de le rendre insensible à la casse !  
 - Correcteur synthaxique !

## Installation

Zsh est en général installer par défaut sur les distribution Linux récentes, cependant si vous ne l'avez pas, installer le avec votre gestionnaire de paquet.

Pour Oh-My-Zsh c'est très simple avec une simple commande `curl` :

```bash
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

Ou si vous préférez, avec `get` :

```bash
sh -c "$(wget -O- https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

Le fichier de configuration se trouve ici : `~/.zshrc`

## Les thèmes

L'avantage avec Oh-My-Zsh c'est la possibilité de personnaliser son shell très simplement avec des thèmes préfait.

Vous pouvez retrouver la liste des thèmes ici :  [Github](https://github.com/ohmyzsh/ohmyzsh/wiki/Themes)

J'ai personnellement une petite préférence pour le thème `agnoster` :

![Agnoster theme](https://cloud.githubusercontent.com/assets/2618447/6316862/70f58fb6-ba03-11e4-82c9-c083bf9a6574.png)

Pour configurer le thème il suffit de modifier la ligne `ZSH_THEME` dans le fichier `~/.zshrc` avec le thème souhaité :

```bash
ZSH_THEME="agnoster"
```

## Les plugins

Comme pour les thèmes, une grande force de Oh-My-Zsh est sa looooongue liste de plugins disponible. Vous pouvez les retrouver ici : [Github](https://github.com/ohmyzsh/ohmyzsh/tree/master/plugins)

Pour les utiliser, vous devez les renseigner dans le fichier de configuration `~/.zshrc` :

```bash
plugins=(
  git
  ansible
  autojump
  syntax-highlighting
  autosuggestions
  zsh-completions
)
```

# Screen

## Présentation

Avez-vous déjà été confronté à une situation où vous exécutez une tâche de longue durée sur une machine distante, et soudainement votre connexion tombe, la session SSH est interrompue et votre travail est perdu. Et bien, cela nous est arrivé à tous à un moment donné, n'est-ce pas? Heureusement, il existe un utilitaire appelé `screen`qui nous permet de reprendre les sessions.

Screen ou GNU Screen est un multiplexeur terminal. En d'autres termes, cela signifie que vous pouvez démarrer une session, puis ouvrir n'importe quel nombre de fenêtres (terminaux virtuels) à l'intérieur de cette session. Les processus exécutés dans Screen continueront de s'exécuter même si vous êtes déconnecté.

## Installation

Le package `screen` est pré-installé sur la plupart des distributions Linux de nos jours. Vous pouvez vérifier s'il est installé sur votre système avec la commande : `screen --version`

```bash
screen --version
Screen version 4.08.00 (GNU) 05-Feb-20
```

Si `screen` n'est pas installé, vous pouvez l'installer facilement avec le gestionnaire de paquet de votre distribution :

### Debian & Ubuntu family

```bash
sudo apt install screen
```

### Fedora & CentOS family

```bash
sudo dnf install screen
```

### Arch & Manjaro family

```bash
sudo pacman -S screen
```

## Utilisation

Pour démarrer une session, il suffit simplement d'utiliser la commande :

```bash
screen
```

Mais je vous conseille plutôt de démarrer vos sessions avec un nom à chaque fois :

```bash
 screen -S <nom_session>
```

Vous pouvez lister toutes les sessions screen en cours avec :

```bash
screen -ls
```

Pour reprendre une session déjà ouverte :

```bash
screen -r <id_session>
```

Pour rejoindre une session déjà utilisée :

```bash
screen -x <id_session>
```

## Raccourcis clavier

Voici un petit récapitulatif des raccourcis clavier pour screen :

| Raccourci | Description |
| --- | --- |
| `Ctrl+a` `c` | Créer une nouvelle fenêtre (avec shell) |
| `Ctrl+a` `"` | Répertorier toutes les fenêtres |
| `Ctrl+a` `<0-9>` | Passer à la fenêtre 0-9 (par numéro) |
| `Ctrl+a` `A` | Renommer la fenêtre actuelle |
| `Ctrl+a` `S` | Diviser la région actuelle horizontalement en deux régions |
| `Ctrl+a` `\|` | Diviser la région actuelle verticalement en deux régions |
| `Ctrl+a` `tab` | Basculer le focus d'entrée sur la région suivante |
| `Ctrl+a` `Ctrl+a` | Basculer entre la fenêtre actuelle et la fenêtre précédente |
| `Ctrl+a` `Q` | Fermer toutes les régions sauf la région actuelle |
| `Ctrl+a` `X` | Fermer la région actuelle |
| `Ctrl+a` `d` | Déconnecter de la session |
| `Ctrl+a` `?` | Afficher les raccourcis disponibles |

## Configuration

Screen est configurable directement via son fichier de configuration dans `/etc/screenrc` et `~/.screenrc`.

# Autres astuces

## Color ls

Un script Ruby qui colorise la sortie ls avec des couleurs et des icônes. 

![](/images/image_2021-05-10_131515.png)

Lien officiel : [Github](https://github.com/athityakumar/colorls)

## Powerlevel10k

Ajoute une personnalisation bien plus poussée du terminal. Compatible avec Oh-My-Zsh.

Lien officiel : [Github](https://github.com/romkatv/powerlevel10k)

### Ajout des informations OpenStack
![p10k-openstack.png](/images/linux/terminal/p10k-openstack.png)

Ajoutez la variable `CLOUD` à votre `openrc` :
```bash
echo "export CLOUD=<cloud>" >> <fichier_openrc> 
```

Ajoutez dans le fichiers `~/.p10k.zsh ` la fonction suivante :
```bash
  function prompt_openstack() {
    if [[ -n $OS_PASSWORD ]]; then
      p10k segment -b blue -f '#ffffff' -i '' -t "$CLOUD |  $OS_PROJECT_NAME |  $OS_USERNAME"
    fi
  }
```
Et modifiez cette ligne `typeset -g POWERLEVEL9K_LEFT_PROMPT_ELEMENTS` en ajoutant `openstack` :
```bash
  typeset -g POWERLEVEL9K_LEFT_PROMPT_ELEMENTS=(
    os_icon                 # os identifier
    dir                     # current directory
    vcs                     # git status
    openstack               # display current openstack config
  )
```

## Bash-completion
Comme son nom l'indique, améliore l'autocompletion de bash

## Atuin
Une meilleure gestion de l'historique bash.

Lien officiel : [Github](https://github.com/ellie/atuin)