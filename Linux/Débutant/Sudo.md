---
title: Linux - Sudo
description: Comprendre et utiliser sudo !
published: true
date: 2021-06-14T07:40:32.894Z
tags: 
editor: markdown
dateCreated: 2021-05-24T10:35:32.558Z
---

![Sudo](https://sudo-labs.github.io/sudolabs/assets/imgs/sudo_logo.png)

# Présentation

La commande sudo vous permet d'exécuter des programmes en tant qu'autre utilisateur, par défaut l'utilisateur root. Si vous passez beaucoup de temps dans votre terminal, sudo est l'une des commandes que vous utiliserez assez fréquemment.

L'utilisation de sudo au lieu de se connecter en tant que root est plus sécurisée car vous pouvez accorder des privilèges administratifs limités à des utilisateurs individuels sans qu'ils connaissent le mot de passe root.

# Installation

Le package sudo est préinstallé sur la plupart des distributions Linux.

Pour vérifier si le package sudo est installé sur votre système, ouvrez votre console, tapez `sudo`et appuyez sur `Enter`. Si sudo a installé le système, un court message d'aide s'affiche. Sinon, vous verrez quelque chose comme “*sudo command not found”*.

Si sudo n'est pas installé, vous pouvez facilement l'installer à l'aide du gestionnaire de packages de votre distribution.

## **Ubuntu et Debian**

```bash
apt install sudo
```

## **CentOS et Fedora**

```bash
yum install sudo
```

## Arch et Manjaro

```bash
pacman -S sudo
```

# Configuration rapide

## **Ajouter un utilisateur aux Sudoers**

Par défaut, sur la plupart des distributions Linux, l'octroi d'un accès sudo est aussi simple que l'ajout de l'utilisateur au groupe sudo défini dans le fichier `/etc/sudoers` . Les membres de ce groupe pourront exécuter n'importe quelle commande en tant que root. Le nom du groupe peut différer d'une distribution à l'autre.

Sur Debian, Ubuntu et leurs dérivés, c'est le groupe `sudo`  :

```bash
usermod -aG sudo username
```

Sur la plupart des autres distributions Linux (incluant Red-Hat et ses dérivés comme CentOS), sur BSD ou Unix c'est le groupe `wheel`  :

```bash
usermod -aG wheel username
```

## Expiration du mot de passe

Par défaut, sudo vous demandera de saisir à nouveau votre mot de passe après cinq minutes d'inactivité sudo. Vous pouvez modifier le délai d'expiration par défaut en modifiant le fichier `/etc/sudoers`. **La modification directe du fichier sudoers est déconseillée**, il vaut mieux éditer le fichier à l'aide de `visudo` (commande installée avec le paquet sudo) qui vérifiera qu'aucune erreur de syntaxe n'a été effectuée avant enregistrement des modifications et bloquera l'édition concurrente du fichier :

```bash
visudo
```

Si vous souhaitez éditer la configuration de sudo sans être logué à root il vous faudra utiliser la commande suivante (si autorisé par les permissions sudo de votre utilisateur) :

```bash
sudo visudo
```

Définissez le délai par défaut en ajoutant la ligne ci-dessous, où `10` est le délai spécifié en minutes, 0 permettra de demander le mot de passe de l'utilisateur à chaque lancement d'une commande avec sudo :

```bash
Defaults timestamp_timeout=10
```

Si vous souhaitez modifier l'horodatage uniquement pour un utilisateur spécifique, ajoutez la ligne suivante, où `NOM_UTILISATEUR` est l'utilisateur en question.

```bash
Defaults:<NOM_UTILISATEUR> timestamp_timeout=10
```

# Utilisation de sudo

La syntaxe de la commande `sudo` est la suivante:

```bash
sudo <OPTIONS> <COMMANDE>
```

La commande `sudo` a de [_nombreuses options_](https://www.sudo.ws/man/1.8.3/sudo.man.html) qui contrôlent son comportement, mais généralement, elle est utilisée dans sa forme la plus basique, sans aucune option.

Pour utiliser sudo, préfixez simplement la commande avec `sudo`:

```bash
sudo <COMMANDE>
```

Où `COMMANDE` est la commande pour laquelle vous souhaitez utiliser sudo.

La première fois que vous utilisez sudo dans une session, vous serez invité à entrer le mot de passe de l'utilisateur et la commande sera exécutée en tant que root.

Exemple : lister les fichiers du dossier root :

```bash
sudo ls /root
```

## **Exécuter une commande en tant qu'utilisateur autre que root**

Beaucoup de personne pense que `sudo` n'est utilisée que pour fournir des autorisations root à un utilisateur standard. En fait, vous pouvez utiliser `sudo` pour exécuter une commande en tant  que n'importe quel utilisateur.

L'option `-u` vous permet d'exécuter une commande en tant qu'un utilisateur spécifique.

Dans l'exemple suivant, nous utilisons `sudo` pour exécuter la commande `whoami` en tant qu'utilisateur «richard» :

```bash
sudo -u richard whoami
```

La commande `whoami` imprimera le nom de l'utilisateur exécutant la commande :

```bash
richard
```

## **Rediriger avec Sudo**

Si vous essayez de rediriger la sortie d'une commande vers un fichier pour lequel votre utilisateur ne dispose d'aucune autorisation d'écriture, vous obtiendrez une erreur «*Permission denied*».

Cela se produit car la redirection « `>`» de la sortie est effectuée sous l'utilisateur auquel vous êtes connecté, et non sous l'utilisateur spécifié avec sudo. La redirection se produit avant que la commande `sudo` ne soit appelée.

Une solution consiste à appeler un nouveau shell en tant que root en utilisant `sudo sh -c`:

```bash
sudo sh -c 'echo "test" > /root/file.txt'
```

## Obtenir la liste des permissions de sudo

Sur des environnements où la configuration de sudo n'est pas celle par défaut, permettant l'utilisation de toutes les commandes avec tous les utilisateurs, il est utile de connaître les droits que l'on peut avoir avec `sudo`. Il suffit d'exécuter la commande suivante pour avoir la liste des permissions de son utilisateur :

```bash
sudo -l
```

# Configuration avancée

## Créer des alias d'utilisateurs

Sudo nous permet de spécifier des alias d'utilisateurs pour les regrouper sous des appellations plus facile à retenir (par exemple DBA pourrait contenir tous les utilisateurs administrateurs de base de données). De plus deux types d'alias utilisateurs existent : les `User_Alias` et les `Runas_Alias`.

Les `User_Alias` permettent de regrouper sous un alias différents utilisateurs ou groupes utilisateurs qui pourront exécuter certaines commandes. Leur création est de cette forme :

```bash
User_Alias <NOM_USER_ALIAS> = <LISTE_USER>
```

Où `NOM_USER_ALIAS` est le nom donné à l'alias. Il doit être composé de lettres en majuscules, de chiffres ou d'un underscore et commencer par une lettre majuscule.

Où `LISTE_USER` est la liste des utilisateurs séparés par des `,`. Les différentes formes que peuvent prendre les utilisateurs sont les suivantes :

-   Nom du login de l'utilisateur : techos
-   ID de l'utilisateur précédé par un `#` : #1000
-   Nom d'un groupe utilisateur précédé par un `%` : %dba
-   ID d'un groupe utilisateur précédé par `%#` : %#1005
-   Nom d'un `User_Alias` précédemment spécifié

Les `Runas_Alias` permettent de regrouper sous un alias différents utilisateurs ou groupes utilisateurs qui seront les utilisateurs ou groupes utilisateurs disponibles pour l'exécution des commandes. Leur création est de cette forme :

```bash
Runas_Alias <NOM_RUNAS_ALIAS> = <LISTE_RUNAS_USER>
```

Où `NOM_RUNAS_ALIAS` est le nom donné à l'alias. Il doit être composé de lettres en majuscules, de chiffres ou d'un underscore et commencer par une lettre majuscule.

Où `LISTE_RUNAS_USER` est la liste des utilisateurs séparés par des `,`. Les différentes formes que peuvent prendre les utilisateurs sont les suivantes :

-   Nom du login de l'utilisateur : root
-   ID de l'utilisateur précédé par un `#` : #0
-   Nom d'un groupe utilisateur précédé par un `%` : %dba
-   ID d'un groupe utilisateur précédé par `%#` : %#1005
-   Nom d'un `Runas_Alias` précédemment spécifié

## Créer des alias d'hôtes

Sudo peut permettre l'application de droits suivant l'hôte sur lequel la commande sudo est exécutée. C'est une fonctionnalité intéressante à partir du moment que la configuration sudoers est reportée sur différentes machines qui ont des rôles différents (serveur Web, serveur de BDD, serveur DNS, etc.). Pour créer un alias `Host_Alias` il faudra l'ajouter à l'aide de `visudo` :

```bash
Host_Alias <NOM_HOTE_ALIAS> = <LISTE_HOTE>
```

Où `NOM_HOTE_ALIAS` est le nom donné à l'alias. Il doit être composé de lettres en majuscules, de chiffres ou d'un underscore et commencer par une lettre majuscule.

Où `LISTE_HOTE` est la liste des hôtes séparés par des `,`. Les différentes formes que peuvent prendre les hôtes sont les suivantes :

-   Nom de l'hôte : SRV0001
-   FQDN : SRV0001.domain.local
-   Adresse IP : 192.168.0.100
-   Sous-réseau IP avec masque de sous-réseau : 192.168.0.0/255.255.255.128
-   Sous-réseau IP au format CIDR : 192.168.0.0/25
-   Nom d'un `Host_Alias` précédemment spécifié

## Créer des alias de commandes

Les alias de commandes permettent de regrouper sous un alias plusieurs commandes qui pourront être ajoutées à des permissions. Ces alias sont à créer sous cette forme :

```bash
Cmnd_Alias <NOM_CMND_ALIAS> = <LISTE_CMND>
```

Où `NOM_CNMD_ALIAS` est le nom donné à l'alias. Il doit être composé de lettres en majuscules, de chiffres ou d'un underscore et commencer par une lettre majuscule.

Où `LISTE_CNMD` est la liste des commandes séparées par des `,`. Les différentes formes que peuvent prendre les commandes sont les suivantes :

-   Le chemin vers une commande : /sbin/visudo
-   Le chemin vers une commande + précisions des arguments autorisés (supporte le wildcard) : /usr/bin/systemctl status \*
-   L'interdiction d'une commande en commençant par `!` : !/usr/bin/passwd \*root\*
-   Nom d'un `Cnmd_Alias` précédemment spécifié

Pour plus de sécurité il est aussi possible de spécifier le hash SHA-2 (sha224, sha256, sha384 ou sha512) du fichier de commande pour que son exécution ne soit possible que si le hash, calculé dynamiquement à l'exécution de sudo, correspond à celui présent dans sudoers. Le hash devant être au format hexadécimale ou base64. Pour réaliser une telle configuration il suffit de renseigner l'alias sous cette forme :

```bash
Cmnd_Alias <NOM_CMND_ALIAS> = <HASH_TYPE>:<HASH> <LISTE_CMND>
```

Où `HASH_TYPE` précise le type de hash utilisé : sha224, sha256, sha384 ou sha512.

Où `HASH` est le résultat du hash calculé sur le fichier de commande.

## Créer de nouvelles permissions

Les permissions dans sudoers ont toutes cette forme :

```bash
<USER> <HOTE>=(<RUNAS_USER>:<RUNAS_GROUP>) <COMMANDE>
```

Où `USER` spécifie l'utilisateur ou les utilisateurs pour qui cette permission les concerne. Il est possible de spécifier plusieurs utilisateurs en les séparant par des `,` et en respectant la [syntaxe évoquée plus haut](https://wiki-tech.io/Linux/Sudo#cr%C3%A9er-des-alias-dutilisateurs) pour la création d'alias `User_Alias`. Il est bien entendu possible d'utiliser des `User_Alias` précédemment créés.

Où `HOTE` spécifie l'hôte ou les hôtes pour lesquels cette permission est appliquée. Il est possible de spécifier plusieurs hôtes en les séparant par des `,` et en respectant la [syntaxe évoquée plus haut](https://wiki-tech.io/Linux/Sudo#cr%C3%A9er-des-alias-dh%C3%B4tes) pour la création d'alias `Host_Alias`. Il est bien entendu possible d'utiliser des `Host_Alias` précédemment créés.

Où `RUNAS_USER` spécifie l'utilisateur ou les utilisateurs avec qui les commandes pourront être exécutées. Il est possible de spécifier plusieurs utilisateurs en les séparant par des `,` et en respectant la [syntaxe évoquée plus haut](https://wiki-tech.io/Linux/Sudo#cr%C3%A9er-des-alias-dutilisateurs) pour la création d'alias `Runas_Alias`. Il est bien entendu possible d'utiliser des `Runas_Alias` précédemment créés.

Où `RUNAS_GROUP` spécifie le group ou les groupes avec qui les commandes pourront être exécutées. Il est possible de spécifier plusieurs groupes en les séparant par des `,` et en respectant la [syntaxe évoquée plus haut](https://wiki-tech.io/Linux/Sudo#cr%C3%A9er-des-alias-dutilisateurs) pour la création d'alias `Runas_Alias`. Il est bien entendu possible d'utiliser des `Runas_Alias` précédemment créés.

Où `COMMANDE` spécifie une ou des commandes qui sont autorisées par cette permission. Il est possible de spécifier plusieurs commandes en les séparant par des `,` et en respectant la [syntaxe évoquée plus haut](https://wiki-tech.io/Linux/Sudo#cr%C3%A9er-des-alias-de-commandes) pour la création d'alias `Cmnd_Alias`. Il est bien entendu possible d'utiliser des `Cnmd_Alias` précédemment créés.

La forme peut se résumer en : Qui Où = (Comme qui) Quoi.

A noter que chacun des éléments précédents peuvent être remplacés par `ALL` s'il n'y a pas besoin d'avoir un filtrage particulier.

Il est aussi possible de rajouter des `TAG` qui s'appliqueront à toutes les commandes suivantes. La forme passe donc à ceci :

```bash
<USER> <HOTE>=(<RUNAS_USER>:<RUNAS_GROUP>) <TAG>: <COMMANDE>
```

Voici deux `TAG` pouvant être utiles :

-   `PASSWD` et `NOPASSWD` : par défaut c'est `PASSWD` qui est actif, ce tag permet de requérir le mot de passe de l'utilisateur à l'exécution de sudo (`PASSWD`) ou de ne jamais lui demander son mot de passe pour l'exécution des commandes permises (`NOPASSWD`).
-   `EXEC` et `NOEXEC` : par défaut c'est `EXEC` qui est actif, ce tag permet d'autoriser l'exécution de commandes (`EXEC`) à l'intérieur d'une commande (avec vim, less ou more il est possible d'exécuter des commandes en les précédant par `!`) ou bien de ne pas autoriser ce fonctionnement (`NOEXEC`).

Il est possible de spécifier plusieurs `TAG` à la suite et leur effet s'appliquera pour une permission jusqu'à ce qu'un `TAG` à l'effet opposé soit défini.

## Vigilance sur la sécurité vis-à-vis des configurations de sudo

Bien que sudo est avantageux et permet d'augmenter la sécurité des machines, du moins lorsqu'on n'utilise pas les paramètres par défaut, une mauvaise configuration de ce dernier et plusieurs accès indésirables pourront s'ouvrir. Les erreurs les plus courantes étant de donner des permissions à des utilisateurs sur des commandes qui peuvent exécuter d'autres commandes, dont un shell. Bien que pouvant être contré en partie avec le tag `NOEXEC`, des utilisations particulières pourront donner l'accès à un shell root ou bien permettre la lecture de fichiers que seul root est sensé pouvoir lire. Il faut tenter de **réduire au maximum les commandes autorisées et de spécifier les options/arguments au maximum**.

En exemple un administrateur souhaite donner l'accès à un utilisateur à la gestion du service sshd pour vérifier son statut et éventuellement le redémarrer :

```bash
techos ALL=(root) /usr/bin/systemctl * sshd
```

L'utilisateur pourra bien vérifier le statut du daemon sshd et le redémarrer mais l'utilisation du wildcard fait que cet utilisateur pourra aussi l'arrêter, le désactiver (disable) et même agir sur d'autres daemon tant que sa commande termine par “sshd”. Pour restreindre l'utilisateur au maximum nous sommes obligés de préciser les deux commandes autorisées sans utiliser de wildcard :

```bash
techos ALL=(root) /usr/bin/systemctl status sshd, /usr/bin/systemctl restart sshd
```

Tout semble cette fois-ci parfait et pourtant le résultat de la commande `sudo systemctl status sshd` peut être réalisée au sein d'un pager, qui utilise `less` (suivant la configuration du système), et qui permet d'ouvrir un shell en tant que root. Avec ce cas  précis l'utilisation du tag `NOEXEC` n'est pas possible car systemctl retournera une erreur lors de son exécution. La documentation de systemctl (obtenable avec `man systemctl` si man a été installé) nous renseigne sur l'existence d'un paramètre pouvant être défini pour ne pas avoir de pager, c'est `--no-pager`. Nous pouvons modifier le sudoers comme ceci :

```bash
 techos ALL=(root) /usr/bin/systemctl --no-pager status sshd, /usr/bin/systemctl restart sshd
```

Autre principe à essayer d'appliquer : si un utilisateur demande à avoir une nouvelle permission afin de pouvoir éditer un fichier dont il n'a pas l'accès, il vaut mieux d'abord chercher à lui donner les droits en lecture et écriture sur le fichier (si c'est possible), plutôt que de lui rajouter une permission avec sudo. Ceci pour trois raisons :

1.  Il y a peut-être une raison pour que cet utilisateur ne puisse pas modifier le fichier demandé, il faudra vérifier dans un premier temps si sa demande est recevable.
2.  La création d'une nouvelle permission pourrait être mal réalisée et l'utilisateur pourrait éditer plus de fichiers qu'il n'en a réellement le droit.
3.  Il pourrait utiliser l'éditeur de texte pour ouvrir un shell root.

Si l'utilisation de sudo semble inévitable alors au lieu de lui fournir un accès avec son éditeur de texte favoris, il vaut mieux lui imposer l'utilisation de `sudoedit` qui s'appuiera sur les variables, dans cet ordre, de l'utilisateur pour le choix de l'éditeur : `SUDO_EDITOR`, `VISUAL` et enfin `EDITOR`. Le contenu de ces variables devant être le chemin complet à un éditeur de texte. L'utilisation de `sudoedit` (ou `sudo -e`) a pour avantage de prémunir de l'utilisation de l'exécution de commande pour ouvrir un shell root car l'utilisateur travaillera sur un fichier temporaire avec ses droits utilisateur à lui. 

Du côté de l'administrateur, s'il souhaite donner l'accès en édition du fichier /etc/resolv.conf, il devra inscrire une permission de ce type :

```bash
techos ALL=(root) /usr/bin/sudoedit /etc/resolv.conf
```