---
title: Ajouter ou étendre un disque à chaud
description: Comment s'y prendre sans impact sur la production
published: true
date: 2021-07-05T17:41:40.628Z
tags: linux, partition, disque, virtualisation
editor: markdown
dateCreated: 2021-07-05T17:41:40.628Z
---

# Quelques réflexions avant d'agir

## Quels sont les risques des commandes utilisées pour l'ajout ou l'extension à chaud ?

Les commandes présentes dans cet article sont inoffensives pour votre serveur, à l'exception des commandes pour gérer les partitions.
Globalement les commandes utilisées permettent soit de récupérer des informations systèmes ou soit de demander au noyau Linux de rafraîchir ses informations sur les matériels connectés.

La phase la plus critique est celle de l'ajout du disque ou de l'extension de disque faite sur le serveur ou l'hyperviseur.
Par conséquence **il est vivement recommandé de réaliser des tests en environnement de qualification** avant de réaliser ces mêmes changements en production. Nous ne pourrons être tenu responsable de vos actions si des conséquences néfastes sur votre production ont eu lieu.

Bien que cette mise en garde puisse vous effrayer, le rédacteur n'a pour le moment jamais rencontré de cas de crash ou de perte de données lors des actions expliquées plus tard.


## Pourquoi vouloir ajouter un disque ou l'étendre à chaud ?

En général ces opérations sont réalisées sur des serveurs qui doivent assurer un service continu et dont un arrêt, même court, n'est pas envisageable.

Et pourquoi sont-elles réalisées ? Cela dépend des besoins de chacun : arriver prochaine à saturation d'un disque, mise à niveau avec des disques plus véloces, restructuration des données sur plusieurs disques, etc.


## Quels sont les prérequis pour l'ajout ou l'extension de disques à chaud ?

Assurez-vous que votre matériel supporte l'ajout de disque à chaud ou que votre superviseur supporte cette fonctionnalité.

> Si vous souhaitez étendre un disque d'une VM il est préférable de le faire sans aucun snapshots, par conséquent la présence d'une sauvegarde récente et fonctionnelle de la VM est fortement recommandée.
{.is-info}

Les commandes indiquées pour l'ajout d'un nouveau disque ne sont valides que pour des contrôleurs SCSI.


# Passer à l'action

> Les sections suivantes supposent que l'action d'ajout ou d'extension de disque a déjà été réalisée et que la VM n'a pas automatiquement mis à jour ses informations matérielles.
{.is-info}

> Les commandes indiquées nécessitent pour la plupart les droits super-utilisateur. Vous pouvez au choix vous connecter en tant que ***root*** ou bien utiliser `sudo` s'il est configuré.
{.is-warning}


## Commandes pour l'ajout d'un disque à chaud

Avant d'effectuer la première action sur le système vérifiez que le disque n'a pas été reconnu automatiquement sans actions particulières grâce à la commande :
``` bash
lsblk 
```

> Les disques sont communément notés **sdX** où X est une lettre pour identifier le disque. A savoir que le préfixe *sd* indique un type de disque avec contrôleur SCSI qui cache un disque SATA, SCA, SAS ou Fibre Channel pour les plus connus. 
> Les contrôleurs IDE remontent avec le préfixe *hd* et les disques virtuels de VirtIO remontent avec le préfixe *vd*.
{.is-info}

Après avoir vérifier que le nouveau disque est bien absent il vous faut lister les différents hôtes d'adaptateur de bus (en anglais *Host Bus Adapter* souvent abrégé en **HBA**). La commande suivante permet de lister tous les HBA :
``` 
ls /sys/class/scsi_host/
```

Normalement vous devriez au moins avoir un HBA noté *host0*. Si d'autres sont présents le chiffre de fin est incrémenté.

Il est temps de demander au système de scanner les différents HBA pour récupérer des informations actualisées avec le disque ajouté :
``` bash
echo "- - -" > /sys/class/scsi_host/host<NUMERO DU HBA>/scan
```

> La commande peut être exécutée sur tous les HBA sans aucun risque, de cette manière vous aurez à coup sûr scanner le bon HBA contenant votre nouveau disque.
{.is-info}

> N'oublier pas de remplacer *\<NUMERO DU HBA>* par un des numéros existants pour les *hostX* listés avec la commande `ls /sys/class/scsi_host/`.
{.is-warning}

A ce stade la commande `lsblk` doit maintenant vous remonter votre nouveau disque !


### C'est trop long à réaliser, y a-t-il une commande unique ?

Il n'y a pas d'autres solutions connues du rédacteur mais il est possible de réaliser les commandes précédentes de manière automatique en scannant l'entièreté des HBA avec le script bash suivant :
``` bash
for HBA in `ls /sys/class/scsi_host/`
do
echo "- - -" > /sys/class/scsi_host/$HBA/scan
done
```

L'écriture pouvant être raccourcie sur une ligne unique :
``` bash
for HBA in `ls /sys/class/scsi_host/`; do echo "- - -" > /sys/class/scsi_host/$HBA/scan; done
```


### Comprendre les commandes précédentes

La commande commençant par `echo "- - -"` demandait au noyau d'analyser l'entièreté d'un HBA et ceci grâce au contenu inscrit dans le fichier scan : "- - -". Cette indication est un wildcard pour signifier de scanner tous les dispositifs connectés au HBA.

Les dispositif sont identifiés par un ID de la forme **H:C:T:L** où :
1. **H** est le numéro du HBA
1. **C** est le canal (ou channel en anglais) utilisé du HBA
1. **T** est la cible (ou target en anglais)
1. **L** est le numéro logique de l'unité (ou *Logical Unit Number* an anglais souvent abrégé LUN)

La commande `echo` indiquait au HBA, identifié par son numéro, de scanner l'ensemble des "C T L", mais nous pouvons affiner le scan en précisant un ID pour chacun, ou certains d'entre eux, à la place du "-" interprété comme un Wildcard.

La commande suivante permet de récupérer les ID HCTL des différents dispositifs rattachés :
``` bash
lsblk -S
```

Si vous aviez déjà plusieurs disques connectés et visibles du système vous auriez pu deviner l'ID des nouveaux périphériques remontés. Avec cet ID vous auriez pu faire un scan précis sur le nouveau disque plutôt que de demander de scanner l'entièreté des dispositifs connectés au HBA.

Par exemple sous Hyper-V est généralement présent un contrôleur SCSI où un disque dur et un lecteur CD sont connectés (pour des VM de génération 2). Le disque dur à pour ID *0:0:0:0* et le lecteur CD *0:0:0:1*. Ces ID confirment bien que les deux périphériques ont été connectés sur le même HBA et qu'en plus ils utilisent le même canal et la même cible.
Lorsque j'ajoute un nouveau disque au contrôleur je peux sélectionner l'emplacement du dispositif qui correspond au LUN. Si le disque est ajouté dans l'emplacement 10, c'est-à-dire le LUN 10, alors il me suffirait de lancer la commande suivante pour remonter le disque dans la VM :
``` bash
echo "0 0 10" > /sys/class/scsi_host/host0/scan
```


## Commandes pour l'extension d'un disque à chaud

> L'extension de disque ne pouvant être fait qu'avec une VM les informations suivantes ne tiendront absolument pas compte des machines physiques.
{.is-warning}

Pour demander au noyau Linux de rescanner votre disque afin que le système mette à jour sa capacité disque tout ce qu'il vous faut connaître est le nom du disque dans Linux.
La plupart du temps les disques sont nommés **sdX** où X est une lettre de l'alphabet. Par contre ils sont toujours présent dans */dev/*.

Pour lister les disques de la machine la commande suivante peut être utilisée :
```
lsblk
```

> A noter que la commande `lsblk` va lister tous les dispositifs block du système, le lecteur de disque pourra lui aussi apparaître, probablement sous l'appellation *sr0*.
{.is-info}

Si plusieurs disques sont remontés, tentez d'identifier le disque ayant reçu l'extension via son ancienne capacité disque.
Une fois le disque identifié la commande suivante est à exécuter pour demander au noyau de rescanner le disque :
``` bash
echo 1 > /sys/class/block/sd<LETTRE DU DISQUE>/device/rescan
```

> N'oubliez pas de remplacer **\<LETTRE DU DISQUE>** par la lettre de votre disque sinon la commande retournera une erreur.
{.is-warning}

> Si vous n'avez pas réussi à identifier votre disque vous pouvez utiliser la commande précédente sur l'ensemble de vos disques.
{.is-info}

Suite à cette action votre disque doit maintenant être vu avec sa nouvelle capacité (vous pouvez le voir avec `lsblk` ou `fdisk -l`). Il ne vous reste plus qu'à utiliser ce nouvel espace disque. Ci-dessous sont indiqués deux cas de figure pouvant être rencontré pour l'extension de disque.


### Etendre le volume physique LVM rattaché au disque

> Les informations suivantes ne seront valides que si le PV LVM prend en compte l'entièreté du disque et non pas une partition présente sur le disque.
> Référez-vous aux informations fournies par `pvdisplay` pour vérifier ce prérequis.
{.is-warning}

L'extension du PV LVM suite à l'extension du disque est très simple et réside en une commande unique :
```
pvresize /dev/sd<LETTRE DU DISQUE>
```

Suite à cette commande vous pourrez vérifier que LVM vous a bien réalloué de nouveaux PE. Il ne vous restera plus qu'à profiter de cette extension de stockage pour étendre des LV existants ou pour en créer de nouveaux.

> L'extension d'un PV va automatiquement étendre le VG qui y est rattaché !
{.is-info}


### Extension d'une partition

> Les commandes suivantes peuvent détruire votre système si vous ne comprenez pas ce que vous faites et que vous le réalisez mal. Les actions sur les partitions n'est pas une opération anodine et peuvent encore être plus dommageable s'il s'agit de la partition système.
> Entrainez-vous au préalable sur des environnements de tests.
{.is-warning}

> L'extension d'une partition n'est possible que si de la place est disponible après la partition. Cette section ne va couvrir uniquement l'extension de partition et non le déplacement d'une partition.
{.is-info}

Du fait que le disque a été étendu il est fort probable que les partitions occupaient l'entièreté de l'espace disponible, l'opération d'extension de partition ne sera possible que sur la dernière partition.

Pour lister vos partitions vous pouvez utiliser les commandes suivantes (en remplaçant *\<LETTRE DU DISQUE>* par la lettre du disque ayant été étendu) :
``` 
lsblk -f /dev/sd<LETTRE DU DISQUE>
```
Ou bien :
``` 
fdisk -l /dev/sd<LETTRE DU DISQUE>
```

Les partitions d'un disque reprennent le nom du disque et y ajoute un chiffre derrière, **sdXY** où :
1. **X**, représente la lettre du disque 
2. **Y**, représente le numéro de la partition 

> La dernière partition n'est pas forcément celle ayant le chiffre le plus élevé !
> Le numéro d'une partition pouvant être défini manuellement lors du partitionnement du disque il faut donc prendre la partition apparaissant en dernière dans la liste.
{.is-warning}

Pour étendre la dernière partition il va falloir réaliser les actions suivantes :
1. Récupérer le numéro du secteur de début de la dernière partition
1. Supprimer la dernière partition
1. Recréer la partition en l'étendant comme souhaité

Afin de réaliser ces opérations les commandes suivantes utiliseront `fdisk`. Pour récupérer le secteur de démarrage de la partition c'est la commande précédemment indiquée qui nous sera utile, l'information étant affichée en deuxième colonne, "Début" ou "Start" :
``` 
fdisk -l /dev/sd<LETTRE DU DISQUE>
```

> La création d'une nouvelle partition avec un secteur de démarrage différent aura pour effet de corrompre votre système de fichier !
> Tout comme le fait de définir un secteur de fin inférieur au secteur de fin originel !
{.is-warning}

Il faut maintenant éditer les partitions du disque grâce à la commande :
``` 
fdisk /dev/sd<LETTRE DU DISQUE>
```

Avec la commande suivante c'est maintenant la CLI de `fdisk` qui se présente à vous. Il faudra réaliser la actions suivantes dans l'ordre indiqué :
1. **d** : commande pour supprimer une partition.
1. **\<NUMERO PARTITION>** : renseigner le numéro de la partition à supprimer, cela vous sera demandé que si plusieurs partitions sont présentent sur le disque.
1. **n** : commande pour créer une nouvelle partition.
1. **\<SECTEUR DE DEBUT>** : il vous faut renseigner le secteur de démarrage de la partition précédemment récupéré, la valeur par défaut proposée devrait normalement correspondre.
1. **\<SECTEUR DE FIN>** : vous pouvez préciser le secteur de fin de la partition. Par défaut l'outil vous propose d'utiliser tout l'espace disponible. A noter que des valeurs de la forme *+50G* sont acceptées pour indiquer que l'on veut une partition de 50Go.
1. **N** : il se peut que `fdisk` vous demande si vous souhaitez supprimer la signature du système de fichier, si tel est le cas répondez non.
1. **p** : affiche la nouvelle table de partition, si les informations affichées vous semblent correctes vous pouvez passer à la suite sinon repasser sur les commandes précédentes.
1. **w** : application des changements et sortie de la CLI de `fdisk`.

> Si à tout moment vous pensez avoir fait une erreur alors n'utiliser pas la dernière commande `w` mais `q` pour quitter sans appliquer les changements. 
{.is-info}

L'extension de la partition étant gérée il ne reste plus qu'à étendre le système de fichier qui y est contenu.


#### Extension du système de fichier ext 

> Cette section ne sera valable que pour des systèmes de fichiers **ext 2, 3 ou 4**, celles-ci étant encore largement utilisées par défaut sur les machines Linux.
{.is-infos}

Si la partition est bien formatée en ext alors la commande suivante pourra étendre le système de fichier jusqu'à occuper l'entièreté de la partition :
```
resize2fs /dev/sd<LETTRE DU DISQUE>
```


---
Rédacteur *Lucas MEYER*