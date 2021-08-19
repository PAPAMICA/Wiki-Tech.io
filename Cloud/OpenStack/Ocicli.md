---
title: Ocicli
description: Installer un cluster Openstack avec ocicli sous Debian
published: true
date: 2021-08-09T12:49:42.791Z
tags: 
editor: markdown
dateCreated: 2021-05-24T10:34:07.034Z
---

![OpenStack — Wikipédia](https://upload.wikimedia.org/wikipedia/commons/thumb/e/e6/OpenStack%C2%AE_Logo_2016.svg/1200px-OpenStack%C2%AE_Logo_2016.svg.png)

# Présentation

OCI (OpenStack Cluster Installer) est un logiciel permettant de provisionner automatiquement des clusters OpenStack. Ce package installe une machine d'approvisionnement, qui utilise les composants ci-dessous:

-   un serveur DHCP (isc-dhcp-server)
-   un serveur de démarrage PXE (tftp-hpa)
-   un serveur web (apache2)
-   un serveur Puppet

Lors du premier des machines du cluster, un système Debian live est proposé en PXE par OCI, pour agir comme une image de découverte. Ce système remonte directement les caractéristiques matérielles à OCI. Les machines peuvent alors être installés avec Debian à partir de ce système, configurés avec un agent Puppet  qui se connectera au master Puppet de OCI. Une fois Debian installé, le serveur redémarre et les services OpenStack sont provisionnés, en fonction du rôle du serveur dans le cluster.

OCI est entièrement intégré à Debian, y compris tous les modules Puppet. Après avoir installé le package OCI et ses dépendances, aucun autre soft n'est necessaire pour l'installation du cluster, ce qui signifie que si un miroir Debian local est disponible, l'installation du cluster OpenStack peut être effectuée complètement hors ligne.

## Les services OpenStack installés

Actuellement, OCI peut installer:

-   Swift (avec des nœuds proxy dédiés en option)
-   Keystone
-   Cinder (backend LVM ou Ceph)
-   Glance (backend File, Swift ou Ceph, Swift peut être externalisé)
-   Heat
-   Horizon
-   Nova (avec prise en charge du GPU)
-   Neutron
-   Barbican
-   Octavia
-   Telemetry (Ceilometer, Gnocchi, Panko, Aodh)
-   Cloudkitty

Des efforts sont actuellement en cours pour intégrer:

-   Magnum
-   Designate

De plus, OCI prend désormais en charge l'exécution de CephOSD sur les nœuds de calcul (ce que l'on appelle «hyper-converged») en tant qu'option pour chaque nœud de calcul. (compute nodes) 

Tout cela avec de la haute disponibilité, en utilisant haproxy et corosync sur les nœuds de contrôleur pour tous les services.

Tous les services utilisent uniquement TLS, même au sein du cluster.

En règle générale, OCI vérifie quel type de nœuds fait partie du cluster et prend des décisions en fonction de celui-ci. Par exemple, s'il existe des nœuds OSD Ceph, OCI utilisera Ceph comme backend pour la sauvegarde Glance, Nova et Cinder. S'il y a des nœuds Cinder Volume, OCI les utilisera avec le backend LVM. S'il existe des nœuds Swiftstore, Swift sera utilisé pour les sauvegardes et les images Glance. S'il existe des nœuds Ceph OSD, mais pas de nœuds Ceph MON dédiés, les contrôleurs agiront comme des moniteurs Ceph. S'il existe des nœuds de calcul, Cinder, Nova et Neutron seront installés sur les nœuds de contrôleur. Etc…

Le nombre minimum de nœuds de contrôleur est de 3, bien qu'il soit possible, avec un peu de bidouille d'installer les 3 contrôleurs sur des machines virtuelles sur un seul serveur (bien sûr, perdre la fonctionnalité de haute disponibilité en cas de panne du matériel).

## Qui est derrière ce projet ?

OCI a été entièrement écrit par Thomas Goirand (zigo). Le travail est entièrement sponsorisé par Infomaniak Networks, qui l'utilise en production dans des clusters raisonnablement grands. Il y a eu quelques contributions sporadiques au sein d'Infomaniak, ainsi que quelques correctifs de contributeurs externes, mais aucune fonctionnalité majeure (pour le moment). Espérons que ce projet, au fil du temps, rassemblera plus de contributeurs.

## Présentation vidéo

Si vous souhaitez avoir une présentation rapide de ce que l'OCI peut faire, pour voir si cela répond à vos besoins, vous pouvez regarder la présentation faite pour le sommet OpenStack en novembre 2020. Ce n'est pas long (19 minutes) : [Youtube](https://www.youtube.com/watch?v=Q25jT2fYDjc) 

# Installation

## Exigences minimales

OCI lui-même fonctionnera bien avec environ 20 Go de disque dur et quelques Go de RAM. Cependant, pour installer OpenStack, vous aurez besoin d'au moins 3 contrôleurs avec un minimum de 16 Go de RAM, 32 Go sont recommandés et le mieux est de 64 Go de RAM. Si vous voulez Ceph, un minimum de 3 Ceph OSD est nécessaire, cependant, nous ne parlons que lorsque votre cluster atteint 100 disques. La recommandation Ceph est qu'un serveur donné ne supprime pas plus de 10% de la capacité totale. Donc, 10 serveurs OSD au début, c'est bien. En ce qui concerne Swift, le nombre minimum de serveurs serait de 3, mais si l'un d'entre eux échoue, vous obtiendrez des délais d'attente. Il est donc probablement préférable de commencer avec au moins 6 nœuds de stockage Swift, et peut-être avec 2 proxies. Pour les autres ressources, c'est à vous de choisir : quelques compute nodes (nœuds de calcul), et probablement 2 nœuds de réseau et quelques nœuds de volume.

Si vous avez l'intention d'exécuter le package openstack-cluster-installer-poc pour faire du développement ou du test OCI dans un environnement virtualisé, nous vous recommandons un serveur unique avec 1 To de disque dur et 256 Go de RAM. Cette configuration est suffisante pour provisionner 19 VM sur lesquelles OpenStack sera installé. Il est possible de fonctionner avec moins, mais alors peu de nœuds seront disponibles, et vous devrez ajuster le nombre de serveurs dans `/etc/oci-poc/oci-poc.conf`.

## Installation du paquet

### Le dépôt du paquet

Le paquet est soit disponible depuis Debian Sid / Buster, soit depuis les référentiels de backport stables d'OpenStack.

### Utilisation d'Extrepo

La nouvelle (meilleure) façon d'utiliser les backports Debian Stable d'OpenStack est d'utiliser extrepo. Extrepo est disponible dans les buster-backports officiels. Voici comment installer OpenStack, par exemple (vous avez besoin du dépôt buster-backports dans votre `sources.list`) :

```bash
apt-get install extrepo
extrepo enable openstack_wallaby
apt-get update
```

Consultez la documentation d'extrepo pour en savoir plus.

### Configuration manuelle des dépôts Debian

Si vous souhaitez utiliser Buster avec OpenStack Train, les dépôts ci-dessous doivent être ajoutés au fichier `sources.list` :

```bash
deb http://buster-train.debian.net/debian buster-train-backports main
deb-src http://buster-train.debian.net/debian buster-train-backports main

deb http://buster-train.debian.net/debian buster-train-backports-nochange main
deb-src http://buster-train.debian.net/debian buster-train-backports-nochange main
```

Vous pouvez remplacer buster ci-dessus par n'importe quelle distribution stable Debian (au moment de l'écriture, seuls Stretch et Buster sont pris en charge), et pareil pour le nom de la version OpenStack du jour.

La clé du dépôt est disponible de cette façon :

```bash
apt-get update
apt-get install --allow-unauthenticated -y openstack-backports-archive-keyring
apt-get update
```

Il existe également un miroir contenant TOUTES les versions d'OpenStack en un seul endroit : [http://osbpo.debian.net/debian/](http://osbpo.debian.net/debian/)

### Installer le paquet

Installez simplement `openstack-cluster-installer` avec :

```bash
apt-get install openstack-cluster-installer
```

### Installer un serveur de base de données

Pour MariaDB :

```bash
apt-get install mariadb-server dbconfig-common
```

Il est possible de créer la base de données et les informations d'identification à la main, ou de laisser OCI le gérer automatiquement avec `dbconfig-common`. Si APT s'exécute en mode non interactif, ou si pendant l'installation, l'utilisateur ne demande pas la gestion automatique de la base de données par `dbconfig-common`, voici comment créer la base de données : 

```bash
apt-get install openstack-pkg-tools
. /usr/share/openstack-pkg-tools/pkgos_func
PASSWORD=$(openssl rand -hex 16)
pkgos_inifile set /etc/openstack-cluster-installer/openstack-cluster-installer.conf database connection "mysql+pymysql://oci:${PASSWORD}@localhost:3306/oci"
mysql --execute 'CREATE DATABASE oci;'
mysql --execute "GRANT ALL PRIVILEGES ON oci.* TO 'oci'@'localhost' IDENTIFIED BY '${PASSWORD}';"
```

Il faut alors s'assurer que la directive "`connection`" dans `/etc/openstack-cluster-installer/openstack-cluster-installer.conf` ne contient pas d'espaces avant et après le signe égal.

### Configurer OCI

Assurez-vous que la base de données est synchronisée (si c'est le cas, vous verrez que'il y a des erreurs dans le tableau) :

```bash
apt-get install -y php-cli
cd /usr/share/openstack-cluster-installer ; php db_sync.php
```

Puis éditez `/etc/openstack-cluster-installer/openstack-cluster-installer.conf` et configurer les options comme vous le souhaitez (par exemple: changer les valeurs du réseau, etc.).

### Générer l'autorité de certification racine d'OCI

Pour supporter TLS, OCI utilise sa propre autorité de certification racine. Le certificat d'autorité de certification racine est distribué sur tous les nœuds du cluster. Pour créer l'autorité de certification racine initiale, il existe un script pour tout faire :

```bash
oci-root-ca-gen
```

À ce stade, vous devriez pouvoir naviguer dans l'interface Web d'OCI : `http://your-ip-address/oci/`

Cependant, vous avez besoin d'un login / pass pour entrer. Il y a un utilitaire shell pour gérer vos noms d'utilisateur. Pour ajouter un nouvel utilisateur, procédez comme suit :

```bash
oci-userdb -a mylogin mypassword
```

Les mots de passe sont hachés en utilisant la fonction PHP `password_hash ()` en utilisant l'algo BCRYPT.

De plus, OCI est capable d'utiliser un Radius externe pour son authentification. Cependant, vous devez toujours ajouter manuellement des connexions dans la base de données. Ce qui est ci-dessous insère un nouvel utilisateur qui a une entrée dans le serveur Radius :

```bash
oci-userdb -r newuser@example.com
```

>  Vous devez également configurer votre adresse de serveur Radius et votre secret partagé dans `openstack-cluster-installer.conf`.
{.is-warning}

> Même s'il existe un système d'authentification, il est fortement conseillé de ne pas exposer OCI à Internet. La meilleure configuration est si votre serveur d'approvisionnement n'est pas du tout accessible de l'extérieur.
{.is-warning}

## Installation des services annexes

### ISC-DHCPD

Configurez `isc-dhcp` pour qu'il corresponde à votre configuration réseau. Notez que "`next-server`" doit être l'adresse de votre nœud master Puppet (c'est-à-dire : le serveur DHCP que nous sommes en train de configurer).

Modifiez `/etc/default/isc-dhcpd` :

```bash
sed -i 's/INTERFACESv4=.*/INTERFACESv4="eth0"/' /etc/default/isc-dhcp-server
```

Puis éditez `/etc/dhcp/dhcpd.conf` :

```bash
allow booting;
allow bootp;
default-lease-time 600;--
max-lease-time 7200;
ddns-update-style none;
authoritative;
ignore-client-uids On;

subnet 192.168.100.0 netmask 255.255.255.0 {
        range 192.168.100.20 192.168.100.80;
        option domain-name "example.com";
        option domain-name-servers 9.9.9.9;
        option routers 192.168.100.1;
        option subnet-mask 255.255.255.0;
        option broadcast-address 192.168.100.255;
        next-server 192.168.100.2;
        if exists user-class and option user-class = "iPXE" {
                filename "http://192.168.100.2/oci/ipxe.php";
        } else {
                filename "lpxelinux.0";
        }
}
```

Notez soigneusement que `192.168.100.2` doit être l'adresse de votre serveur OCI, car il sera utilisé pour servir PXE, TFTP et Web pour les nœuds esclaves. Il est bien sûr très bien d'utiliser une autre adresse si votre serveur OCI le fait, alors n'hésitez pas à adapter ce qui précède à votre guise.

Notez qu'à partir de la version 28 d'OCI et au-dessus, le chargement de l'initrd et du noyau se fait via HTTP, donc l'utilisation de `lpxelinux.0` est obligatoire (`pxelinux.0` ne devrait plus être utilisé, car il ne supporte que TFTP).

De plus, pour qu'OCI autorise les requêtes à partir de la plage DHCP, vous devez ajouter vos sous-réseaux DHCP à `TRUSTED_NETWORKS` dans `openstack-cluster-installer.conf`. Sinon, le reporting matériel ne fonctionnera jamais.

### tftpd

Configurez `tftp-hpa` pour servir les fichiers depuis OCI:

```bash
sed -i 's#TFTP_DIRECTORY=.*#TFTP_DIRECTORY="/var/lib/openstack-cluster-installer/tftp"#' /etc/default/tftpd-hpa
```

Puis redémarrez `tftpd-hpa`.

## Préparation de l'installation des serveurs

### Configuration des clés SSH

Lors de la configuration, OCI créera une paire de clés ssh publique / privée ici:

```bash
/etc/openstack-cluster-installer/id_rsa
```

Une fois cela fait, il copiera le contenu `id_rsa.pub` correspondant dans:

```bash
/etc/openstack-cluster-installer/authorized_keys
```

et ajoutera également toutes les clés publiques qu'il trouve sous `/root/.ssh/authorized_keys`. Plus tard, ce fichier sera copié dans l'image live OCI Debian, et dans tous les nouveaux systèmes qu'OCI installera. OCI utilisera plus tard la clé privée qu'il a générée pour se connecter aux serveurs, tandis que vos clés seront également présentes afin que vous puissiez vous connecter à chaque serveur individuel en utilisant votre clé privée. Par conséquent, il est fortement conseillé de personnaliser `/etc/openstack-cluster-installer/allowed_keys` *_avant_ de* construire l'image OCI Debian Live.

### Construire l'image en direct d'OCI

```bash
mkdir -p /root/live-image
cd /root/live-image
openstack-cluster-installer-build-live-image --pxe-server-ip 192.168.100.2 --debian-mirror-addr http://deb.debian.org/debian --debian-security-mirror-addr http://security.debian.org/
cp -auxf /var/lib/openstack-cluster-installer/tftp/* /usr/share/openstack-cluster-installer
cd ..
rm -rf /root/live-image
```

Il est possible d'utiliser des serveurs proxy de paquets comme `approx`, ou des miroirs locaux, ce qui donne la possibilité de déconnecter complètement votre cluster et OCI lui-même d'Internet.

### Configurer l'ENC de Puppet

Une fois le service master Puppet installé, ses directives de classificateur de nœud externe (ENC) doivent être définies, de sorte que OCI agisse comme ENC (ce qui signifie que OCI définira les rôles et les classes de puppet à appeler lors de l'installation d'un nouveau serveur avec puppet) :

```bash
. /usr/share/openstack-pkg-tools/pkgos_func
pkgos_add_directive /etc/puppet/puppet.conf master "external_nodes = /usr/bin/oci-puppet-external-node-classifier" "# Path to enc"
pkgos_inifile set /etc/puppet/puppet.conf master external_nodes /usr/bin/oci-puppet-external-node-classifier
pkgos_add_directive /etc/puppet/puppet.conf master "node_terminus = exec" "# Tell what type of ENC"
pkgos_inifile set /etc/puppet/puppet.conf master node_terminus exec
```

Puis redémarrez le service Puppet-Master.

### Facultatif: approx

Pour accélérer le téléchargement du paquet, il est fortement recommandé d'installer approx  localement sur votre serveur de provisionnement OCI et d'utiliser son adresse lors de la configuration des serveurs (l'adresse est définie dans `/etc/openstack-cluster-installer/openstack-cluster-installer.conf`).

# Utiliser OCI

## Démarrage des serveurs

Démarrez plusieurs ordinateurs, en les bootant avec PXE. Si tout se passe bien, ils attraperont le DHCP de l'OCI et redémarreront l'image en direct Debian de l'OCI. Une fois le serveur en marche, un agent s'exécutera pour faire un rapport à l'interface Web d'OCI. Rafraîchissez simplement l'interface Web d'OCI et vous verrez des machines. Vous pouvez également utiliser l'outil CLI:

```bash
apt-get install openstack-cluster-installer-cli
ocicli machine-list
	serial   ipaddr          memory  status     lastseen             cluster  hostname
	2S2JGM2  192.168.100.37  4096    live       2018-09-20 09:22:31  null
	2S2JGM3  192.168.100.39  4096    live       2018-09-20 09:22:50  null
```

Notez qu'ocicli peut soit utiliser un login / mot de passe qui peut être défini dans la base de données interne de l'OCI, soit l'adresse IP du serveur sur lequel ocicli s'exécute peut être inscrite dans la liste blanche dans `/etc/openstack-cluster-installer/openstack-cluster-installer.conf`.

## Création de régions, d'emplacements, de réseaux, de rôles et de clusters Swift

### Avant de commencer

Dans cette documentation, tout se fait via la ligne de commande en utilisant ocicli. Cependant, absolument tout peut également être fait à l'aide de l'interface Web. Il est simplement plus facile d'expliquer l'utilisation de l'interface de ligne de commande, car cela évite d'avoir à afficher des screenshots de l'interface Web.

Ici, le seul réseau que vous ajouterez à OCI serait les réseaux internes d'OpenStack. Jamais, vous n'ajouterez les réseaux publics ou ceux des VM OpenStack. Par exemple, un réseau pour la gestion des nœuds, un pour `vm-net`, un pour le réseau `ceph-cluster` ... Tous les réseaux que vous utiliserez sur OpenStack, doivent être provisionnés avec OpenStack lui-même à l'aide de l'API OpenStack .

### Création de régions et d'emplacements Swift

Avant d'installer les systèmes sur vos serveurs, des clusters doivent être définis. Cela commence par la configuration des régions Swift. Dans un cluster Swift, il existe des zones et des régions. Lors du téléchargement d'un fichier sur Swift, il est répliqué sur N zones (généralement 3). Si 2 régions sont définies, Swift essaie de répliquer des objets sur les deux régions.

Sous OCI, vous devez d'abord définir les régions Swift. Pour ce faire, cliquez sur "Swift region" sur l'interface web, ou en utilisant ocicli, saisissez :

```bash
ocicli swift-region-create datacenter-1
ocicli swift-region-create datacenter-2
```

Créez ensuite des emplacements associés à ces régions:

```bash
ocicli location-create dc1-zone1 datacenter-1
ocicli location-create dc1-zone2 datacenter-1
ocicli location-create dc2-zone1 datacenter-2
```

Plus tard, lors de l'ajout d'un nœud de données Swift à un cluster (les nœuds de données sont les serveurs qui effectueront réellement le stockage Swift), un emplacement doit être sélectionné.

Une fois les emplacements définis, il est temps de définir les réseaux. Les réseaux sont également rattachés à des emplacements. Les zones et régions Swift seront liées à ces emplacements et régions.

### Créer des réseaux

```bash
ocicli network-create dc1-net1 192.168.101.0 24 dc1-zone1 no
```

La commande ci-dessus créera un sous-réseau `192.168.101.0/24,` situé dans `dc1-zone1`. Créons 2 réseaux supplémentaires:

```bash
ocicli network-create dc1-net2 192.168.102.0 24 dc1-zone2 no
ocicli network-create dc2-net1 192.168.103.0 24 dc2-zone1 no
```

Ensuite, pour que le cluster soit accessible, créons un réseau public sur lequel les clients se connecteront:

```bash
ocicli network-create pubnet1 203.0.113.0 28 public yes
```

Notez que si vous utilisez un /32, il sera configuré sur l'interface `lo` de votre contrôleur. La configuration attendue consiste à utiliser BGP pour acheminer cette adresse IP publique sur le contrôleur. Pour ce faire, il est possible de personnaliser l'ENC et d'ajouter le peering BGP à votre routeur. Voir à la fin de cette documentation pour cela.

### Créer un nouveau cluster

Créons un nouveau cluster:

```bash
ocicli cluster-create swift01 example.com
```

Maintenant que nous avons un nouveau cluster, les réseaux que nous avons créés peuvent y être ajoutés:

```bash
ocicli network-add dc1-net1 swift01 all eth0
ocicli network-add dc1-net2 swift01 all eth0
ocicli network-add dc2-net1 swift01 all eth0
ocicli network-add pubnet1 swift01 all eth0
```

Lors de l'ajout du réseau public, automatiquement, une adresse IP sera réservée au VIP (Virtual Private IP). Cette adresse IP sera plus tard partagée par les nœuds du contrôleur, pour effectuer la haute disponibilité (HA), contrôlée par pacemaker / corosync. Le principe est le suivant: si l'un des nœuds du contrôleur héberge le VIP (et il est affecté à son eth0), et devient indisponible (disons, le serveur plante ou le câble réseau est débranché), alors le VIP est réaffecté au eth0 d'un autre nœud de contrôleur du cluster.

Si vous sélectionnez 2 interfaces réseau (par exemple, eth0 et eth1), la liaison sera utilisée. Notez que votre équipement réseau (commutateurs, etc.) doit être configuré en conséquence (LACP, etc.), et que la configuration de ces équipements sort du cadre de cette documentation. Consultez votre fournisseur d'équipement réseau pour plus d'informations.

## Véritable certificat pour l'API

Par défaut, OCI générera un certificat auto-signé pour tout. Bien que cela fonctionne bien à quelques exceptions près (cela ne fonctionne visiblement pas pour Heat, Magnum et si l'on veut activer le chiffrement sur disque Swift), il est préférable, en production, d'utiliser un vrai certificat API, afin que les clients puissent faites confiance à votre serveur. Pour ce faire, il faut d'abord choisir un nom d'hôte pour l'API. Ceci est défini de cette façon:

```bash
ocicli cluster-set z --vip-hostname cloud-api.example.com
```

Une fois cela fait, dans le serveur OCI, générez un certificat pour ce nom d'hôte:

```bash
oci-gen-slave-node-cert cloud-api.example.com
```

Le `cd` vers `/var/lib/oci/ssl/slave-nodes/cloud-api.example.com`. Ensuite, vous pouvez trouver le c`loud-api.example.com.csr` (.csr signifie certificat de signature de certificat) qui peut être utilisé pour opter pour un vrai certificat. Faites signer le certificat, puis remplacez les fichiers `.crt` et `.pem` par le vrai contenu signé. Si vous réutilisez un certificat générique, vous souhaitez probablement également remplacer le fichier `.key`. Notez que le fichier `.pem` doit contenir le certificat *_et_* la clé privée, concaténés, et peut-être aussi tous les certificats intermédiaires.

Une fois cela fait, informez simplement OCI que nous utilisons un vrai certificat signé:

```bash
ocicli cluster-set z --self-signed-api-cert no
```

Désormais, Puppet sera démarré sans utiliser la racine ca de l'OCI comme environnement, et ca\_file ne sera pas utilisé dans tous les fichiers de configuration d'OpenStack (une chaîne vide sera définie à la place).

Si vous avez mis votre cluster en production avant de signer le certificat, il est possible d'utiliser, sur le serveur de Puppet, l'utilitaire `oci-update-cluster-certs` :

```bash
oci-update-cluster-certs z
```

Cela remplacera le certificat `cloud-api.example.com` partout dans le cluster et redémarrera les services pour l'utiliser. Cet utilitaire shell est également utile chaque fois que votre certificat SSL expire et doit être mis à jour.

## Inscription de serveurs dans un cluster

Maintenant que nous avons des réseaux affectés au cluster, il est temps d'ajouter des serveurs d'attribution au cluster. Disons que nous avons la sortie ci-dessous:

```bash
ocicli machine-list
	serial  ipaddr          memory  status  lastseen             cluster  hostname
	C1      192.168.100.20  8192    live    2018-09-19 20:31:57  null
	C2      192.168.100.21  8192    live    2018-09-19 20:31:04  null
	C3      192.168.100.22  8192    live    2018-09-19 20:31:14  null
	C4      192.168.100.23  5120    live    2018-09-19 20:31:08  null
	C5      192.168.100.24  5120    live    2018-09-19 20:31:06  null
	C6      192.168.100.25  5120    live    2018-09-19 20:31:14  null
	C7      192.168.100.26  4096    live    2018-09-19 20:31:18  null
	C8      192.168.100.27  4096    live    2018-09-19 20:31:26  null
	C9      192.168.100.28  4096    live    2018-09-19 20:30:50  null
	CA      192.168.100.29  4096    live    2018-09-19 20:31:00  null
	CB      192.168.100.30  4096    live    2018-09-19 20:31:07  null
	CC      192.168.100.31  4096    live    2018-09-19 20:31:20  null
	CD      192.168.100.32  4096    live    2018-09-19 20:31:28  null
	CE      192.168.100.33  4096    live    2018-09-19 20:31:33  null
	CF      192.168.100.34  4096    live    2018-09-19 20:31:40  null
	D0      192.168.100.35  4096    live    2018-09-19 20:31:47  null
	D1      192.168.100.37  4096    live    2018-09-21 20:31:23  null
	D2      192.168.100.39  4096    live    2018-09-21 20:31:31  null
```

Ensuite, nous pouvons inscrire des machines dans le cluster de cette manière:

```bash
ocicli machine-add C1 swift01 controller dc1-zone1
ocicli machine-add C2 swift01 controller dc1-zone2
ocicli machine-add C3 swift01 controller dc2-zone1
ocicli machine-add C4 swift01 swiftproxy dc1-zone1
ocicli machine-add C5 swift01 swiftproxy dc1-zone2
ocicli machine-add C6 swift01 swiftproxy dc2-zone1
ocicli machine-add C7 swift01 swiftstore dc1-zone1
ocicli machine-add C8 swift01 swiftstore dc1-zone2
ocicli machine-add C9 swift01 swiftstore dc2-zone1
ocicli machine-add CA swift01 swiftstore dc1-zone1
ocicli machine-add CB swift01 swiftstore dc1-zone2
ocicli machine-add CC swift01 swiftstore dc2-zone1
```

En conséquence, il y aura 1 contrôleur, 1 proxy Swift et 2 nœuds de données Swift sur chaque zone de nos clusters. Les adresses IP seront automatiquement attribuées aux serveurs lorsque vous les ajoutez aux clusters. Ils ne sont pas affichés dans ocicli, mais vous pouvez les vérifier via l'interface Web. Le résultat devrait être comme ceci:

```bash
ocicli machine-list
	serial  ipaddr          memory  status  lastseen             cluster  hostname
	C1      192.168.100.20  8192    live    2018-09-19 20:31:57  7        swift01-controller-1.example.com
	C2      192.168.100.21  8192    live    2018-09-19 20:31:04  7        swift01-controller-2.example.com
	C3      192.168.100.22  8192    live    2018-09-19 20:31:14  7        swift01-controller-3.example.com
	C4      192.168.100.23  5120    live    2018-09-19 20:31:08  7        swift01-swiftproxy-1.example.com
	C5      192.168.100.24  5120    live    2018-09-19 20:31:06  7        swift01-swiftproxy-2.example.com
	C6      192.168.100.25  5120    live    2018-09-19 20:31:14  7        swift01-swiftproxy-3.example.com
	C7      192.168.100.26  4096    live    2018-09-19 20:31:18  7        swift01-swiftstore-1.example.com
	C8      192.168.100.27  4096    live    2018-09-19 20:31:26  7        swift01-swiftstore-2.example.com
	C9      192.168.100.28  4096    live    2018-09-19 20:30:50  7        swift01-swiftstore-3.example.com
	CA      192.168.100.29  4096    live    2018-09-19 20:31:00  7        swift01-swiftstore-4.example.com
	CB      192.168.100.30  4096    live    2018-09-19 20:31:07  7        swift01-swiftstore-5.example.com
	CC      192.168.100.31  4096    live    2018-09-19 20:31:20  7        swift01-swiftstore-6.example.com
	CD      192.168.100.32  4096    live    2018-09-19 20:31:28  null
	CE      192.168.100.33  4096    live    2018-09-19 20:31:33  null
	CF      192.168.100.34  4096    live    2018-09-19 20:31:40  null
	D0      192.168.100.35  4096    live    2018-09-19 20:31:47  null
	D1      192.168.100.37  4096    live    2018-09-21 20:31:23  null
	D2      192.168.100.39  4096    live    2018-09-21 20:31:31  null
```

Comme vous pouvez le voir, les noms d'hôte sont également calculés automatiquement.

## Calcul du ring Swift

Avant de commencer à installer les serveurs, le ring Swift doit être construit. Exécutez simplement cette commande:

```bash
ocicli swift-calculate-ring swift01
```

Notez que cela peut prendre très longtemps, en fonction de la taille de votre cluster.C'est normal, soyez juste patient.

## Installation des serveurs

Il n'y a pas (encore) de gros bouton «installer le cluster» sur l'interface Web ou sur la CLI. Au lieu de cela, les serveurs doivent être installés un par un:

```bash
ocicli machine-install-os C1
ocicli machine-install-os C2
ocicli machine-install-os C3
```

Il est conseillé d'installer d'abord les nœuds de contrôleur, de vérifier manuellement qu'ils sont correctement installés (par exemple, vérifier que "`openstack user list`" fonctionne), puis les nœuds de stockage Swift, puis les nœuds proxy Swift. Cependant, les nœuds du même type peuvent être installés en même temps. De plus, en raison de l'utilisation d'un VIP et d'un corosync / pacemaker, les nœuds de contrôleur *_doivent_* être installés à peu près en même temps.

Il est également possible de voir les dernières lignes du journal d'installation d'un serveur à l'aide de l'interface de ligne de commande:

```bash
ocicli machine-install-log C1
```

Cela affichera les journaux de l'installation du système à partir de `/var/log/oci`, puis une fois que le serveur aura redémarré, il affichera les journaux de Puppet depuis `/var/log/puppet-first-run`.

## Vérification de votre installation

Connectez-vous sur un nœud de contrôleur. Pour ce faire, listez son IP:

```bash
CONTROLLER_IP=$(ocicli machine-list | grep C1 | awk '{print $2}')
ssh root@${CONTROLLER_IP}
```

Une fois connecté au contrôleur, vous verrez les informations de connexion sous `/root/oci-openrc.sh`. Trouvez-le et essayez:

```bash
. /root/oci-openrc.sh
openstack user list
```

Vous pouvez également essayer Swift:

```bash
. /root/oci-openrc.sh
openstack container create foo
echo "test" >bar
openstack object create foo bar
rm bar
openstack object delete foo bar
```

## Activation du chiffrement d'objets Swift

Localement sur le store Swift, Swift stocke l'objet sous une forme claire. Cela signifie que toute personne ayant un accès physique au centre de données peut extraire un disque dur et que les objets sont accessibles à partir du dossier `/srv/node`. Pour atténuer ce risque, Swift peut chiffrer les objets qu'il stocke. Les métadonnées (comptes, containters, etc.) seront toujours stockées sous une forme claire, mais au moins, les données stockées chiffrées.

La façon dont cela est implémenté dans OCI consiste à utiliser Barbican. C'est la raison pour laquelle Barbican est provisionné par défaut sur les nœuds du contrôleur. Par défaut, le chiffrement n'est pas activé. Pour l'activer, vous devez d'abord stocker la clé de chiffrement d'objet dans le store Barbican. Cela peut être fait de cette façon:

```bash
ENC_KEY=$(openssl rand -hex 32)
openstack secret store --name swift-encryption-key \
  --payload-content-type=text/plain --algorithm aes \
  --bit-length 256 --mode ctr --secret-type symmetric \
  --payload ${ENC_KEY}
  
	+---------------+--------------------------------------------------------------------------------------------+
	| Field         | Value                                                                                      |
	+---------------+--------------------------------------------------------------------------------------------+
	| Secret href   | https://swift01-api.example.com/keymanager/v1/secrets/6ba8dd62-d752-4144-b803-b32012d707d0 |
	| Name          | swift-encryption-key                                                                       |
	| Created       | None                                                                                       |
	| Status        | None                                                                                       |
	| Content types | {'default': 'text/plain'}                                                                  |
	| Algorithm     | aes                                                                                        |
	| Bit length    | 256                                                                                        |
	| Secret type   | symmetric                                                                                  |
	| Mode          | ctr                                                                                        |
	| Expiration    | None                                                                                       |
	+---------------+--------------------------------------------------------------------------------------------+
```

Une fois cela fait, l'ID de clé (ici: `6ba8dd62-d752-4144-b803-b32012d707d0`) doit être entré dans l'interface Web de l'OCI, dans la définition du cluster, sous "ID de clé de chiffrement Swift (vide: pas de chiffrement) :". Une fois que cela est fait, une autre exécution de Puppet est nécessaire sur les nœuds proxy Swift:

```bash
OS_CACERT=/etc/ssl/certs/oci-pki-oci-ca-chain.pem puppet agent --test --debug
```

Cela devrait activer le chiffrement. Notez que la clé de cryptage doit être stockée dans Barbican sous les services utilisateur swift et project, afin que Swift y ait accès.

## Correction de node1 inutile dans corosync

Parfois, "`node1`" apparaît lors de l'exécution de "`crm status`". Pour nettoyer cela, faites simplement:

```bash
crm_node -R node1 --force
```

## Correction de ceph -s

Cela corrige tous les avertissements Ceph après une configuration:

```bash
ceph osd pool application enable glance rbd
ceph osd pool application enable nova rbd
ceph osd pool application enable cinder rbd
ceph osd pool application enable gnocchi rbd
ceph osd pool application enable cinderback rbd
ceph mon enable-msgr2
```

## Variable de configuration initiale du cluster

Pour éviter de faire trop de choses lorsque le cluster est en production (comme, par exemple, démarrer MySQL pour faire la configuration initiale du cluster Galera), OCI a une variable appelée "`initial-cluster-setup`". Elle est activée par défaut lors des premières exécutions, et une fois que tous les contrôleurs ont signalé une exécution réussie à Puppet, cette variable est automatiquement définie sur `no`. Voici une liste (probablement non exhaustive) de choses qu'OCI ne fait que si i`nitial-cluster-setup` est défini sur `yes` :

-   ressource openstack-api-vip dans corosync
-   Cluster Galera
-   Faire en sorte que les contrôleurs rejoignent le cluster rabbitmq
-   Utilisateurs du domaine Heat et Magnum
-   Configuration des cellules Nova v2

A tout moment, il est possible de basculer la valeur sur `yes` ou `no` :

```bash
ocicli cluster-set z --initial-cluster-setup no
```

Cependant, il est fortement conseillé de définir la valeur sur no une fois que le cluster est en production.

> Si les 3 contrôleurs de vos clusters exécutent avec succès puppet à la première startup, ils appelleront "`oci-report-puppet-success`". Une fois le troisième contrôleur fait, `initial-cluster-setup` sera automatiquement défini sur la valeur «`no`» dans la base de données OCI.
{.is-info}

## Ajout d'autres types de nœuds

OCI peut gérer, par défaut, les types de nœuds ci-dessous:

-   cephmon : moniteur Ceph
-   cephosd : machines de données Ceph
-   compute : nœuds Nova compute et Neutron DVR
-   controller : nœuds de contrôle OpenStack, exécutant toutes les API et démons
-   swiftproxy : serveurs proxy Swift
-   swiftstore : machines de données Swift
-   volume : nœuds Cinder LVM
-   réseau : routage DHCP, SNAT IPv4 et IPv6

Il est seulement obligatoire d'installer 3 contrôleurs, puis tout le reste est facultatif. Il n'y a rien à configurer, OCI comprendra ce que l'utilisateur veut en fonction du type de nœuds mis à disposition.

Si les nœuds `cephosd` sont déployés, alors tout utilisera Ceph :

-   Nova (c'est-à-dire : `/var/lib/nova/instances` sur Ceph)
-   Glance (images stockées sur Ceph)
-   Cinder (`cinder-volume` déployé sur les nœuds de calcul utilisera le backend Ceph)

Même avec Ceph, la configuration de nœuds de volume ajoutera la capacité de backend LVM. Avec ou sans nœuds de volume, si certains nœuds OSD sont déployés, cinder-volume et cinder-backup avec le backend Ceph seront installés sur les nœuds de calcul.

La migration en direct des machines virtuelles entre les nœuds de calcul n'est possible que si vous utilisez Ceph (c'est-à-dire si certains nœuds Ceph OSD sont déployés), ou si vous utilisez l'option `--block-migration`.

Les nœuds Ceph MON sont facultatifs. S'ils ne sont pas déployés, le Ceph MON et MGR seront installés sur les nœuds du contrôleur.

Les nœuds de réseau sont facultatifs. S'ils ne sont pas déployés, les contrôleurs agiront en tant que nœuds de routage SNAT et IPv6, et les serveurs DHCP seront installés sur les nœuds de calcul.

# Utilisation avancée

## Configuration d'adresse IPMI automatisée

Étant donné que la gestion manuelle de cela peut prendre trop de temps, OCI offre la possibilité de configurer automatiquement les adresses IPMI de tous les serveurs découverts. Et comme il est possible que dans la configuration de votre réseau, il y ait plusieurs réseaux IPMI en fonction de l'emplacement physique du serveur, OCI offre la possibilité de choisir automatiquement un réseau IPMI en fonction du réseau DHCP qu'un serveur démarre sur l'image Live Debian.

La première chose à faire est de définir un réseau IPMI, de le définir avec le rôle "`ipmi`", puis de le faire correspondre à l'adresse IP du réseau DHCP:

```bash
ocicli network-create ipmi 192.168.200.0 24 zone-1 no
ocicli network-set ipmi --role ipmi --ipmi-match-addr 192.168.100.0 --ipmi-match-cidr 24
```

Une fois cela fait, l'option `automatic_ipmi_numbering = yes` doit être définie dans `/etc/openstack-cluster-installer/openstack-cluster-installer.conf`.

Lorsque cette option est définie, chaque fois qu'un serveur signale sa configuration matérielle, OCI vérifie s'il possède une IP IPMI correcte. Sinon, OCI effectuera un ssh dans le serveur et exécutera les commandes "`ipmitool`" nécessaires pour définir une configuration réseau valide. Ce faisant, l'adresse IP sera réservée dans la table "ips" de l'OCI, en veillant à ce que jamais, une adresse IP ne soit utilisée deux fois.

Avec l'exemple ci-dessus, si un serveur PXE démarre sur le réseau 192.168.100.0/24, une adresse IP IPMI lui sera automatiquement attribuée sur le réseau 192.168.200.0/24. Notez que le mot de passe IPMI est choisi au hasard. Comme nous utilisons openssl rand -base64, il peut être judicieux de vous assurer que votre serveur OCI a une bonne source d'entropie.

Si auparavant, certains serveurs avaient leur adresse IPMI déjà définie sur quelque chose qui correspond au réseau IPMI, mais que OCI ne l'a pas enregistrée, il est possible d'obtenir cette adresse IP enregistrée dans la base de données d'OCI. Il suffit de taper cette commande pour le faire :

```bash
ocicli ipmi-assign-check
```

Cette commande demandera à OCI de parcourir chaque machine enregistrée dans la base de données et de vérifier l'adresse IPMI détectée. Si cette adresse existe dans la base de données, rien n'est fait. Sinon, un nouvel enregistrement sera ajouté à la base de données pour cette machine, pour éviter un conflit d'adresse ultérieur.

## MAJ automatique du BIOS et du micrologiciel IPMI

La mise à niveau du BIOS et du micrologiciel IPMI des serveurs peut prendre beaucoup de temps si vous gérez un grand nombre de serveurs. OCI offre donc la possibilité d'effectuer ces mises à niveau automatiquement. Ceci est contrôlé à l'aide d'un fichier de configuration qui peut être trouvé ici: `/etc/openstack-cluster-installer/oci-firmware-upgrade-config.json`. Voici un exemple de fichier de configuration valide:

```json
{
	"CL2800 Gen10": {
		"BIOS": {
			"version": "2.1.0",
			"script": "/root/hp-bios-upgrade-2.1.0"
			},
		"IPMI": {
			"version": "2.22",
			"script": "/root/hp-ipmi-upgrade-2.22"
			}
	},
}
```

Avec ce qui précède, si OCI trouve un serveur `HP Cloud Line CL2800` dont le micrologiciel BIOS est inférieur à `2.1.0`, il tentera de le mettre à niveau en lançant le script `/root/hp-bios-upgrade-2.1.0`. Pour ajouter ledit script, l'image live doit être personnalisée. Pour ce faire, ajoutez simplement quelques fichiers dans le dossier `/etc/openstack-cluster-installer/live-image-additions`. Tous les fichiers qui s'y trouvent seront ajoutés à l'image live. Ensuite, l'image live doit être régénérée :

```bash
openstack-cluster-installer-build-live-image
```

Une fois que cela est fait, redémarrez les serveurs qui doivent être mis à jour. À mesure qu'ils démarrent sur l'image live, la mise à jour sera effectuée. Pour référence, voici un exemple de script `hp-bios-upgrade-2.1.0`, qui sera sauvegardé ici: `/etc/openstack-cluster-installer/live-image-additions/root/hp-bios-upgrade-2.1.0`.

```bash
#!/bin/sh

set -e
set -x

cd /root
tar -xvzf CL2600_CL2800_Gen10_BIOS_v2.1.0_11052019_Linux.tgz
cd CL2600_CL2800_Gen10_BIOS_v2.1.0_11052019_Linux/FlashTool/
./flash_bios.sh
reboot
sleep 20000
```

Le "`sleep 20000`" permet de s'assurer que l'agent OCI ne redémarre pas avant le redémarrage de la machine. Cela dépend de la mise à jour à effectuer.

## Personnalisation du /etc/hosts de tout votre cluster

Il est possible d'ajouter des entrées sur tous les `/etc/hosts` des clusters, si vous ajoutez des entrées à ce fichier sur le serveur OCI:

`/etc/openstack-cluster-installer/hosts_append`

Tout ce que génère OCI se trouve entre ces balises:

```bash
# OCISTA_MAINTAINED: Do not touch between these lines, this is a generated content.
... some generated content ...
# OCIFIN_MAINTAINED: Do not touch between these lines, this is a generated content.
```

Ensuite, il est possible d'ajouter manuellement des entrées à chaque `/etc/hosts` après la balise ci-dessus, et ces entrées seront conservées.

## Personnalisation de l'ENC

Dans `/etc/openstack-cluster-installer/hiera`, vous trouverez 2 dossiers et un fichier `all.yaml`. Celles-ci doivent permettre de personnaliser la sortie de l'ENC de l'OCI. Par exemple, si vous mettez:

```yaml
   ntp:
      servers:
         - 0.us.pool.ntp.org iburst
```

dans `/etc/openstack-cluster-installer/hiera/all.yaml`, alors tous les nœuds seront configurés avec ntp en utilisant `0.us.pool.ntp.org` pour synchroniser l'heure.

Si nous avons un cluster swift01, la structure complète des dossiers est la suivante:

```bash
/etc/openstack-cluster-installer/hiera/roles/controller.yaml
/etc/openstack-cluster-installer/hiera/roles/swiftproxy.yaml
/etc/openstack-cluster-installer/hiera/roles/swiftstore.yaml
/etc/openstack-cluster-installer/hiera/nodes/-hostname-of-your-node-.yaml
/etc/openstack-cluster-installer/hiera/all.yaml
/etc/openstack-cluster-installer/hiera/clusters/swift01/roles/controller.yaml
/etc/openstack-cluster-installer/hiera/clusters/swift01/roles/swiftproxy.yaml
/etc/openstack-cluster-installer/hiera/clusters/swift01/roles/swiftstore.yaml
/etc/openstack-cluster-installer/hiera/clusters/swift01/nodes/-hostname-of-your-node-.yaml
/etc/openstack-cluster-installer/hiera/clusters/swift01/all.yaml
```

## Personnalisation du serveur installé au moment de la configuration

Parfois, il est souhaitable de configurer un serveur au moment de l'installation. Par exemple, il peut être nécessaire de configurer le routage (à l'aide de BGP) pour que l'adresse IP virtuelle soit disponible au moment de la configuration. OCI offre tout ce qui est nécessaire pour enrichir la configuration du serveur au moment de l'installation, avant même que l'agent Puppet ne démarre.

Supposons que vous souhaitiez configurer swift01-controller-1 dans votre cluster swift01, y ajouter quagga et ajouter des fichiers de configuration. Créez simplement le dossier, remplissez-y le contenu et ajoutez un fichier `oci-packages-list` :

```bash
mkdir -p /var/lib/oci/clusters/swift01/swift01-controller-1.example.com/oci-in-target
cd /var/lib/oci/clusters/swift01/swift01-controller-1.example.com
echo -n "quagga,tmux" >oci-packages-list
mkdir -p oci-in-target/etc/quagga
echo "some conf" >oci-in-target/etc/quagga/bgpd.conf
```

Lorsque OCI provisionne le serveur baremetal, il vérifie si le fichier `oci-packages-list` existe. Si tel est le cas, les packages sont ajoutés lors de l'installation. Ensuite, le contenu `oci-in-target` est copié dans le système cible.

## Utilisation d'un BGP VIP

De la même manière, vous pouvez par exemple décider d'avoir le VIP de vos contrôleurs pour utiliser le routage BGP. Pour ce faire, écrivez dans `/etc/openstack-cluster-installer/roles/controller.yaml` :

```yaml
   quagga::bgpd:
      my_asn: 64496,
      router_id: 192.0.2.1
      networks4:
         - '192.0.2.0/24'
      peers:
         64497:
            addr4:
               - '192.0.2.2'
            desc: TEST Network
```

Cependant, vous souhaiterez peut-être le faire uniquement pour un nœud spécifique d'un seul cluster de serveurs, plutôt que pour tous. Dans ce cas, utilisez simplement ce schéma de chemin de fichier : `/etc/openstack-cluster-installer/clusters/cloud1/nodes/cloud1-controller-1.example.com.yaml`

Pour tous les contrôleurs du cluster cloud1, utilisez : `/etc/openstack-cluster-installer/clusters/cloud1/roles/controller.yaml`

## Faire un test dans les manifestes OCI à des fins de débogage

Si vous souhaitez tester un changement dans les fichiers de marionnettes de l'OCI, éditez-les dans `/usr/share/puppet/modules/oci`, puis sur le master run, par exemple :

```bash
puppet master --compile swift01-controller-1.example.com
/etc/init.d/puppet-master stop
/etc/init.d/puppet-master start
```

puis sur `swift01-controller-1.example.com` vous pouvez exécuter :

```bash
OS_CACERT=/etc/ssl/certs/oci-pki-oci-ca-chain.pem puppet agent --test --debug
```

## Personnalisation des fichiers et des packages de vos serveurs

Si vous souhaitez personnaliser le contenu des fichiers de vos hôtes, écrivez simplement n'importe quel fichier, par exemple :

```bash
/var/lib/oci/clusters/swift01/swift01-controller-1.example.com/oci-in-target
```

et il sera copié sur le serveur que vous installerez.

De la même manière, vous pouvez ajouter des packages supplémentaires à votre serveur en ajoutant leurs noms dans ce fichier :

```bash
/var/lib/oci/clusters/swift01/swift01-controller-1.example.com/oci-packages-list
```

Les packages doivent être répertoriés sur une seule ligne, séparés par des virgules. Par exemple :

```bash
quagga,bind
```

### Activer Hiera pour l'environnement

Si vous devez activer Hiera, vous pouvez le faire de cette façon :

```bash
mkdir -p /etc/puppet/code/environments/production/manifests/
echo "hiera_include('classes')" > /etc/puppet/code/environments/production/manifests/site.pp
cat /etc/puppet/code/hiera/common.yaml
---
classes:
  - xxx
...
```

# Une fois le déploiement prêt

Il y a actuellement quelques problèmes qui doivent être résolus manuellement. Espérons que tous ces éléments seront automatisés dans un proche avenir. En attendant, veuillez contribuer aux correctifs si vous découvrez comment, ou procédez simplement comme indiqué ci-dessous.

## Réparation des contrôleurs

Malheureusement, parfois, il y a des problèmes de planification dans Puppet. Si cela se produit, on peut essayer de relancer Puppet :

```bash
OS_CACERT=/etc/ssl/certs/oci-pki-oci-ca-chain.pem puppet agent --test --debug 2>&1 | tee /var/log/puppet-run-1
```

Faites-le d'abord sur le nœud du contrôleur 1, attendez qu'il se termine, puis redémarrez-le sur les autres nœuds de contrôleur.

## Ajout de règles de pare-feu personnalisées

OCI utilise `puppet-module-puppetlabs-firewall` et vide les iptables à chaque exécution. Par conséquent, si vous avez besoin de règles de pare-feu personnalisées, vous devez également le faire via Puppet. Si vous souhaitez appliquer les mêmes règles de pare-feu sur tous les nœuds, modifiez simplement `site.pp` comme ceci dans `/etc/puppet/code/environments/production/manifests/site.pp` :

```ruby
hiera_include('classes')

firewall { '000 allow monitoring network':
  proto       => tcp,
  action      => accept,
  source      => "10.3.50.0/24",
}
```

Notez que la règle de pare-feu est précédée d'un nombre. Ceci est obligatoire. Assurez-vous également que ce numéro n'entre pas en conflit avec une règle déjà existante.

Ce que fait OCI, c'est : protéger le VIP du contrôleur (lui refuser l'accès de l'extérieur) et protéger les ports swiftstore pour les serveurs de comptes, de conteneurs et d'objets contre toute requête ne provenant pas du cluster. Ainsi, ce qui précède permettra à un serveur de surveillance à partir de 10.3.50.0/24 de surveiller votre swiftstore.

Si vous souhaitez appliquer ce qui précède uniquement à un nœud spécifique, il est possible de le faire en ne faisant correspondre que certains noms d'hôte. Voici un exemple simple, avec une adresse IP différente autorisée en fonction des rôles de la machine :

```bash
hiera_include('classes')

node /^z-controller.*/ {
  firewall { '000 allow monitoring network':
    proto       => tcp,
    action      => accept,
    source      => "10.1.2.0/24",
  }
}

node default {
  firewall { '000 allow monitoring network':
    proto       => tcp,
    action      => accept,
    source      => "10.3.4.0/24",
  }
}
```

## Ajout de nœuds de calcul

Avec la dernière version d'OCI, ceci est effectué automatiquement : après qu'un nœud de calcul exécute puppet avec succès, il appelle `oci-report-puppet-success`, qui contacte le nœud d'approvisionnement, qui à son tour ssh à l'un des contrôleurs pour exécuter "`nova -manage cell_v2 Discover_hosts` ". Donc, ce qui suit n'est nécessaire que si le nœud de calcul ne s'est pas installé correctement directement.

Pour ajouter le nœud de calcul au cluster et vérifier qu'il est là, sur le contrôleur, procédez comme suit:

```bash
 . oci-openrc
 su nova -s /bin/sh -c "nova-manage cell_v2 discover_hosts"
 openstack hypervisor list
	+----+-------------------------------+-----------------+---------------+-------+
	| ID | Hypervisor Hostname           | Hypervisor Type | Host IP       | State |
	+----+-------------------------------+-----------------+---------------+-------+
	|  4 | swift01-compute-1.example.com | QEMU            | 192.168.103.7 | up    |
	+----+-------------------------------+-----------------+---------------+-------+
```

Il n'y a rien de plus ... :)

## Ajout de la prise en charge du GPU dans un nœud de calcul

Actuellement, seuls les cartes Nvidia sont prises en charge. Tout d'abord, localisez votre GPU dans votre hôte de calcul. Voici un exemple avec une carte Nvidia T4 :

```bash
lspci -nn | grep -i nvidia
	5e:00.0 3D controller [0302]: NVIDIA Corporation TU104GL [Tesla T4] [10de:1eb8] (rev a1)
```

Lorsque vous avez cela, entrez-le simplement avec ocicli :

```bash
ocicli machine-set 1CJ9FV2 --use-gpu yes --gpu-vendor-id 10de --gpu-produc-id 1eb8 --gpu-name nvidia-t4 --gpu-device-type type-PF --vfio-ids 10de:1eb8+10de:0fb9
```

Veuillez noter que les identifiants dans `--vfio-ids` doivent être séparés par `+` et non par une virgule (la conversion est effectuée plus tard par OCI et Puppet).

De plus, le type de périphérique `--gpu` dépend du type de carte GPU et du micrologiciel que vous utilisez. Par exemple, les anciens micrologiciels Nvidia T4 nécessitent le type-PCI, tandis que les nouveaux micrologiciels nécessitent le type-PF. Si vous faites une erreur ici, le nova-scheduler ne saura pas où créer une VM et retournera "`no valid host`".

Cela remplira `/etc/modprobe.d/blacklist-nvidia.conf` pour mettre sur liste noire le pilote Nvidia et quelques autres, `/etc/modules-load.d/vfio.conf` pour charger le module `vfio-pci`, et `/etc/modprobe.d/vfio.conf` avec ce contenu (pour permettre d'exposer des appareils aux invités):

```bash
options vfio-pci ids=10de:1eb8,10de:0fb9
```

Le fichier `/etc/default/grub` doit ensuite être modifié à la main pour ajouter ceci (manuellement) :

```bash
intel_iommu=on
```

Redémarrez la machine de calcul, appliquez Puppet à la fois sur le nœud de calcul et sur les contrôleurs.

Maintenant, créons l'image Glance et Nova pour utiliser ce nouveau GPU et démarrer l'instance:

```bash
openstack image set bionic-server-cloudimg-amd64_20190726_GPU --property img_hide_hypervisor_id='true'
openstack flavor create --ram 6144 --disk 20 --vcpus 2 cpu2-ram6-disk20-gpu-nvidia-t4
openstack flavor set cpu6-ram20-disk20-gpu-t4 --property pci_passthrough:alias=nvidia-t4:1
openstack server create --image bionic-server-cloudimg-amd64_20190726_GPU --nic net-id=demo-net --key-name demo-keypair --flavor cpu6-ram20-disk20-gpu-nvidia-t4 my-instance-with-gpu
```

Dans l'instance, nous pouvons utiliser Cuda et le vérifier:

```bash
wget http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/cuda-repo-ubuntu1804_10.1.168-1_amd64.deb
apt-get update
apt-get install cuda cuda-toolkit-10-1  nvidia-cuda-toolkit
cat /proc/driver/nvidia/version
	NVRM version: NVIDIA UNIX x86_64 Kernel Module  430.26  Tue Jun  4 17:40:52 CDT 2019
	GCC version:  gcc version 7.4.0 (Ubuntu 7.4.0-1ubuntu1~18.04.1)
```

## Plusieurs backends Cinder LVM

Si vous utilisez plusieurs types de backend LVM (par exemple, SSD et HDD), il peut être utile de sélectionner le nom du backend lors de la configuration d'un nouveau nœud de volume. Ceci est fait de cette façon:

```bash
ocicli machine-set 1CJ9FV2 --lvm-backend-name HDD_1
```

Vous pouvez également avoir plusieurs backends sur un seul serveur. Dans ce cas, il est possible d'utiliser un seul backend par lecteur, au lieu de tous les utiliser sur un seul VG. Pour ce faire, faites quelque chose comme ceci:

```bash
ocicli machine set 5KC2J63 --cinder-separate-volume-groups yes --cinder-enabled-backends LVM_SDA:LVM_SDB:LVM_SDC
```

Cela configurera de nouveaux types de volume LVM\_SDA, LVM\_SDB et LVM\_SDC. Pour revenir à la manière normale (c'est-à-dire: un gros VG), il est possible de remettre la valeur de non-remplacement:

```bash
ocicli machine-set 5KC2J63 no-override
```

Attention cependant, OCI ne fera ce qu'il faut qu'une seule fois, lors du provisionnement du système.

# Automatisation avancée

## Installation entièrement automatisée

Lors de la gestion de grands clusters, l'approvisionnement matériel peut prendre une longue partie de votre temps. Il n'y a malheureusement aucun moyen de compresser le temps nécessaire à l'installation physique du matériel, mais OCI est là pour fournir une installation complète sans même avoir à taper une seule ligne de commande.

Les nœuds matériels sont d'abord démarrés dans l'environnement Live, leur matériel est ensuite découvert, et s'il correspond à un profil matériel défini (par vous) dans OCI, le serveur peut être entièrement provisionné sans aucune action humaine.

Si l'on souhaite automatiser entièrement le provisionnement, voici la liste des directives à définir dans `/etc/openstack-cluster-installer/openstack-cluster-installer.conf` :

```bash
[megacli]
megacli_auto_clear=yes
megacli_auto_clear=yes
megacli_auto_clear_num_of_discovery=3
megacli_auto_apply=yes
megacli_auto_apply_num_of_discovery=7

[ipmi]
automatic_ipmi_numbering=yes
automatic_ipmi_username=ocirox

[dns_plugin]
call_dns_shell_script=yes

[root_pass_plugin]
call_root_password_change=yes

[monitoring_plugin]
call_monitoring_plugin=yes

[auto_provision]
auto_add_machines_to_cluster=yes
auto_add_machines_cluster_name=cluster1
auto_add_machines_num_of_discovery=9

[auto_racking]
auto_rack_machines_info=yes
auto_rack_machines_num_of_discovery=7

[auto_install_os]
auto_install_machines_os=yes
auto_install_machines_num_of_discovery=15
```

Notez que tout ce qui précède est défini sur `no` par défaut.

Sur ce qui précède, nous pouvons voir quelques directives avec "`num_of_discovery`". Ce qui se passe, c'est que lorsqu'une machine démarre dans l'image Live OCI, l'openstack-cluster-installer-agent s'exécute en boucle, toutes les 30 secondes (en fait, à tout moment pendant une période de 30 secondes, car le script attend au hasard pour éviter d'envoyer le rapport d'agent de découverte à OCI en même temps ... mais je m'écarte ici ...). Chaque fois que l'agent OCI signale une configuration matérielle pour un serveur, un compteur est incrémenté. C'est notre "`num_of_discovery`". Comme les valeurs de "`num_of_discovery`" sont différentes, une liste d'actions s'effectue sur les serveurs nouvellement découverts. Par exemple, avec les valeurs par défaut, voici la planification (voir ci-dessous pour les détails de chaque opération) :

-   configuration d'IPMI
-   effacement de la configuration RAID
-   application de la "`machine-set`"
-   application du profil RAID
-   récupérer les informations LLDP pour remplir OCI (serveur dc, rack, U ...)
-   ajout d'un serveur au cluster par défaut avec le bon rôle
-   installer le système d'exploitation et redémarrer le serveur

Notez que les valeurs par défaut de "n`um_of_discovery`" sont correctes, et il n'est pas conseillé de les changer à moins que vous ne soyez vraiment sûr de ce que vous faites. Par exemple, il est normal qu'un cycle de découverte matérielle est laissé entre «l'effacement de la configuration RAID» et «l'application du profil RAID», et la découverte LLDP est laissée après de nombreuses exécutions de l'agent car LLDP peut parfois prendre du temps.

Pour réinitialiser le nombre de compteur de découverte:

```bash
ocicli machine-report-counter-reset SERIAL
```

## Racking automatique

OCI s'appuie sur le protocole LLDP pour découvrir à quel switch un serveur est connecté et utilise ces informations pour indiquer où il se trouve et quoi faire. Vos noms de switch vers les informations de mise en rack sont définis dans un fichier Json statique dans `/etc/openstack-cluster-installer/auto-racking.json`. C'est fait de cette façon, car on ne s'attend pas à ce que ces données changent avec le temps.

Ce fichier contient 3 sections principales:

-   `productnames`
-   `switchhostnames`
-   `switchportnames`

Sous les `productnames`, il n'y a actuellement qu'une description du nombre d'unités de rack dont un serveur a besoin.

OCI suppose que chaque serveur de chaque U sera connecté au numéro de port du switch correspondant. Par exemple, le serveur en U-4 sera connecté au port 4 du switch, conformément au LLDP de votre switch.

OCI lira ensuite la description des noms de produits pour indiquer le nombre d'unités de rack qu'un serveur prend.

OCI suppose également que chacun de vos switch utilisera LLDP pour publier les noms et les ports des commutateurs, et que chaque switch est défini avec un nom d'hôte unique dans vos centres de données.

Prenons un exemple. Disons que nous avons un switch numéro 5, dans le rack 3 de la ligne b, dans le Datacenter 2. Prenons le nom d'hôte `dc2-b3-5`. Nous définirons ensuite dans `/etc/openstack-cluster-installer/auto-racking.json` :

```json
"switchhostnames": {
    "dc2-b3-5": {
        "dc": "2",
        "row": "b",
        "rack": "3",
        "location-name": "zone-3",
        "compute-aggregate": "AZ3"
    },
```

Ce qui précède indique que tout ce qui est connecté à ce switch sera provisionné dans la zone d'emplacement 3 d'OCI (selon le paramètre d'emplacement "`ocicli machine-add`"), et s'il s'agit d'un serveur de calcul Nova, il peut être utilisé dans un agrégat nommé AZ3. Cela sera utilisé ci-dessous.

Pour pouvoir déboguer, quelques commandes sont disponibles:

```bash
ocicli machine-guess-racking SERIAL
```

Cela dira où la machine est en rack, étant donné les informations dans le fichier `auto-racking.json` et les informations LLDP publiées par le commutateur.

```bash
ocicli machine-auto-rack SERIAL
```

Sert à remplir les informations de mise en rack.

```bash
ocicli machine-auto-add SERIAL
```

Ajoutera le serveur à l'emplacement défini dans `auto-racking.json` et avec le rôle défini dans le profil matériel.

## Profils matériels

Pour pouvoir prendre des décisions, OCI doit détecter automatiquement le matériel et le faire correspondre à un profil matériel. OCI prend un matériel donné et se compare à la liste des profils. Chaque fois que quelque chose ne correspond pas, un profil matériel est supprimé de la liste. Si l'utilisateur a correctement conçu les profils matériels, à la fin, il ne reste qu'un seul profil. Dans ce cas, le rôle défini dans ce profil peut être utilisé et le profil RAID appliqué à l'aide de MegaCli.

Voici un exemple:

```json
    "compute-with-var-lib-nova-instance": {
        "role": "compute",
        "product-name": [
            "PowerEdge R640",
        ],
        "ram": {
            "min": 256,
            "max": 512
            },
        "hdd": {
            "controller": "megacli",
            "hdd-num-exact-match": "yes",
            "layout": {
                "0": {
                    "raid-type": 1,
                    "software-raid": "no",
                    "options": "WB RA Direct",
                    "size_min": 220,
                    "size_max": 250,
                    "num_min": 2,
                    "num_max": 2
                },
                "1": {
                    "raid-type": 1,
                    "software-raid": "no",
                    "options": "WB RA Direct",
                    "size_min": 800,
                    "size_max": 1800,
                    "num_min": 2,
                    "num_max": 4
                }
            }
        },
        "machine-set": [ "--use_ceph_if_available no --cpu-mode custom --cpu-model Skylake-Server-IBRS"],
        "after-puppet-controller-command": [
            "openstack compute service set --disable %%HOSTNAME%%",
            "openstack aggregate add host %%COMPUTE_AGGREGATE%% %%HOSTNAME%%",
            "openstack aggregate add host INTEL_COMPUTE %%HOSTNAME%%"
            ]
    },
```

Le profil ci-dessus ne correspondra qu'aux machines avec le nom de produit «PowerEdge R640», avec entre 256 et 512 Go de RAM, un contrôleur RAID LSI, avec exactement 2 disques système de 220 à 250 Go et 2 à 4 disques de données de 800 à 1800 GB. Lorsque le profil RAID est appliqué, il fournira 2 matrices RAID1, une pour le système avec les plus petits disques et une autre plus grande qui sera utilisée plus tard dans `/var/lib/nova/instances`.

Ce qui est dans `machine-set` sont des commandes ocicli à émettre lorsque le profil matériel est reconnu. Sur l'exemple ci-dessus, nous pouvons voir que nous configurons un modèle de CPU en fonction du profil matériel. Évidemment, on peut définir un autre profil matériel pour "PowerEdge R6525" (c'est une machine AMD) avec un modèle de CPU différent, par exemple.

Le contenu de la commande `after-puppet-controller-command` sera émis une fois que la première exécution de Puppet aura réussi. N'hésitez pas à y ajouter n'importe quelle commande OpenStack, sachant que `%% HOSTNAME %%` sera remplacé par le FQDN réel du serveur provisionné, et `%% COMPUTE_AGGREGATE %%` sera remplacé par tout ce qui est défini dans `auto-racking.json`. Ici, nous utilisons le profil matériel pour définir la machine dans un agrégat `INTEL_COMPUTE`, car ce cluster possède également des nœuds de calcul AMD. Nous utilisons également `%% COMPUTE_AGGREGATE %%` pour définir automatiquement la bonne zone de disponibilité.

Pour vérifier quel profil matériel correspond à un serveur donné, on peut taper :

```bash
ocicli machine-guessed-profile SERIAL
```

Il est également possible d'appliquer manuellement un profil RAID avec :

```bash
ocicli machine-megacli-reset-raid SERIAL
ocicli machine-megacli-apply SERIAL
```

> Attention à ne pas faire ce qui précède sur un serveur en production.
{.is-warning}

## Plug-in DNS

OCI peut appeler votre propre script personnalisé pour publier les noms d'hôte des nœuds dans votre DNS. A vous de l'écrire. Le script sera appelé chaque fois que des serveurs sont ajoutés à un cluster (automatiquement ou manuellement).

Pour tester le plugin DNS, il est possible de l'appeler manuellement en utilisant :

```bash
ocicli machine-to-dns HOSTNAME
```

## Plug-in de mot de passe root

Lorsqu'une machine est déclarée comme installée, il est possible de définir automatiquement un mot de passe pour elle. Ce mot de passe peut être enregistré quelque part (par exemple en utilisant hashicorp vault, ou un simple fichier texte), en utilisant le script du plugin.

Pour tester le plugin de mot de passe root, une fois qu'une machine est installée, il est possible de l'appeler manuellement en utilisant :

```bash
ocicli machine-gen-root-pass HOSTNAME
```

## Plugin de supervision

OCI ne fournit pas de surveillance, mais si vous avez un tel service, par exemple Zabbix, vous pouvez appeler un script de plugin pour enregistrer les machines dans votre solution de supervision.

Pour appeler manuellement le plugin d'enregistrement de surveillance, on peut taper :

```bash
ocicli machine-to-monitoring HOSTHANE
```

# Gérer le déploiement d'OpenStack

## Activer la classification cloudkitty

Tout d'abord, ajoutez le rôle rating à l'utilisateur cloudkitty :

```bash
openstack role add --user cloudkitty --project services rating
```

Ensuite, activez le module hashmap :

```bash
cloudkitty module enable hashmap
cloudkitty module set priority hashmap 100
```

Notez que l'erreur 503 peut être simplement ignorée, elle fonctionne toujours, comme le montre la liste des modules. Maintenant, ajoutons une note pour les instances :

```bash
cloudkitty hashmap group create instance_uptime_flavor
cloudkitty hashmap service create compute
cloudkitty hashmap field create 96a34245-83ae-406b-9621-c4dcd627fb8e flavor
```

L'ID ci-dessus est celui du service de `hashmap create`. Ensuite, nous réutilisons l'`ID du champ create` que nous venons d'avoir pour le paramètre `-f`, et l'`ID de groupe` pour le paramètre `-g` ci-dessous:

```bash
cloudkitty hashmap mapping create --field-id ce85c041-00a9-4a6a-a25d-9ebf028692b6 --value demo-flavor -t flat -g 2a986ce8-60a3-4f09-911e-c9989d875187 0.03
```

## Création de sondes pour la facturation

Dans cet exemple, nous voulons facturer n'importe quel port sur un réseau spécifique appelé "`ext-net1`" qui contient des adresses IP publiques. Pour ce faire, nous avons besoin d'un `ceilometer-polling`, dans les 3 contrôleurs, pour interroger l'API Neutron toutes les 5 minutes, et demander tous les ports utilisant le réseau "`ext-net1`". Chaque port associé à un projet OpenStack aura besoin d'un enregistrement personnalisé dans la série chronologique Gnocchi.

Donc, tout d'abord, nous devons concevoir notre sonde (c'est-à-dire: l'élément qui interrogera l'API). Disons que lorsque nous faisons cela :

```bash
openstack port list --network ext-net1 --long --debug
```

le mode de débogage montre que nous pouvons traduire cela en cette requête curl :

```bash
curl -g -X GET "https://pub1-api.cloud.infomaniak.ch/network/v2.0/ports?network_id=5a7f5f53-627c-4d0e-be89-39efad5ac54d" \
	-H "Accept: application/json" -H "User-Agent: openstacksdk/0.50.0 keystoneauth1/4.2.1 python-requests/2.23.0 CPython/3.7.3" \
	-H "X-Auth-Token: "$(openstack token issue --format value -c id) | jq .
```

l'API OpenStack répondant de cette façon :

```json
{
  "ports": [
    {
      "id": "c558857c-d010-41ba-8f93-08c3cb876ebe",
      "name": "",
      "network_id": "5a7f5f53-627c-4d0e-be89-39efad5ac54d",
      "tenant_id": "ac4fafd60021431585bbb23470119557",
      "mac_address": "fa:16:3e:d5:3f:13",
      "admin_state_up": true,
      "status": "ACTIVE",
      "device_id": "0c2b0e8f-0a59-4d81-9545-fd90dc7fee73",
      "device_owner": "compute:b4",
      "fixed_ips": [
        {
          "subnet_id": "615ddc30-2ed5-4b0a-aba7-acb19b843276",
          "ip_address": "203.0.113.14"
        },
        {
          "subnet_id": "2c7d6ee4-d317-4749-b6a5-339803ac01f2",
          "ip_address": "2001:db8:1:1::2e8"
        }
      ],
      "allowed_address_pairs": [],
      "extra_dhcp_opts": [],
      "security_groups": [
        "5d9b69fb-2dae-4ed2-839c-91f645d53eeb",
        "c901c534-fd90-4738-aa6b-007cd7a5081b"
      ],
      "description": "",
      "binding:vnic_type": "normal",
      "binding:profile": {},
      "binding:host_id": "cl1-compute-8.example.com",
      "binding:vif_type": "ovs",
      "binding:vif_details": {
        "connectivity": "l2",
        "port_filter": true,
        "ovs_hybrid_plug": true,
        "datapath_type": "system",
        "bridge_name": "br-int"
      },
      "port_security_enabled": true,
      "qos_policy_id": null,
      "qos_network_policy_id": null,
      "resource_request": null,
      "ip_allocation": "immediate",
      "tags": [],
      "created_at": "2021-02-25T08:57:30Z",
      "updated_at": "2021-02-25T09:42:47Z",
      "revision_number": 8,
      "project_id": "ac4fafd60021431585bbb23470119557"
    }
  ]
}
```

Nous créons ensuite le type de ressource correspondant dans Gnocchi :

```bash
gnocchi resource-type create -a status:string:true:max_length=3 -a device_id:uuid:false -a mac_address:string:true:max_length=20  network.ports.ext-net1
gnocchi resource-type create -a status:string:false:max_length=3 -a mac_address:string:false:max_length=20 public_ip
gnocchi resource-type create -a cidr:string:false:max_length=4 -a network_id:uuid:false -a description:string:false:max_length=64 public_subnet
```

Dans `/etc/openstack-cluster-installer/pollsters.d`, nous écrivons simplement un nouveau fichier qui ressemble à ceci :

```bash
---

- name: "network.ports.ext-net1"
  sample_type: "gauge"
  unit: "ip"
  endpoint_type: "network"
  url_path: "/network/v2.0/ports?network_id=5a7f5f53-627c-4d0e-be89-39efad5ac54d"
  value_attribute: "status"
  response_entries_key: "ports"
  project_id_attribute: "project_id"
  value_mapping:
    ACTIVE: "1"
  metadata_fields:
    - "mac_address"
    - "device_id"
    - "device_owner"
    - "fixed_ips"
    - "binding:vnic_type"
    - "binding:host_id"
    - "binding:vif_type"
    - "created_at"
    - "updated_at"
```

Le `url_path` ci-dessus correspond à ce que nous écrivons dans la requête curl. Le `response_entries_key` est le nom de l'objet de niveau supérieur l'objet json auquel Neutron répond. Ecrire ceci dans `/etc/openstack-cluster-installer/pollsters.d/ext-net-ports.yaml` est la seule chose nécessaire. OCI écrira automatiquement ce fichier dans `/etc/ceilometer/pollsters.d` dans les nœuds du contrôleur, et listera cette sonde dans `/etc/ceilometer/polling.yaml`.

## Installer une première image OpenStack

```bash
wget http://cdimage.debian.org/cdimage/openstack/current-9/debian-9-openstack-amd64.qcow2
openstack image create \
	--container-format bare --disk-format qcow2 \
	--file debian-9-openstack-amd64.qcow2 \
	debian-9-openstack-amd64
```

## Mise en réseau

Il existe de nombreuses façons de gérer la mise en réseau dans OpenStack. Cette documentation ne couvre que rapidement un seul moyen, vous pouvez retrouver une documentation plus complète ici : [/Openstack/Réseaux](/Openstack/Réseaux). Cependant, le lecteur doit savoir que OCI installe des nœuds de calcul à l'aide de DVR (Distributed Virtual Routers), ce qui signifie qu'un routeur Neutron est installé sur tous les nœuds de calcul. En outre, OpenVSwitch est utilisé, en utilisant VXLan entre les nœuds de calcul. Quoi qu'il en soit, voici une façon de configurer le réseau. Quelque chose comme ça peut le faire :

```bash
# Create external network
openstack network create --external --provider-physical-network external --provider-network-type flat ext-net
openstack subnet create --network ext-net --allocation-pool start=192.168.105.100,end=192.168.105.199 --dns-nameserver 84.16.67.69 --gateway 192.168.105.1 --subnet-range 192.168.105.0/24 --no-dhcp ext-subnet

# Create internal network
openstack network create --share demo-net
openstack subnet create --network demo-net --subnet-range 192.168.200.0/24 --dns-nameserver 84.16.67.69 demo-subnet

# Create router, add it to demo-subnet and set it as gateway
openstack router create demo-router
openstack router add subnet demo-router demo-subnet
openstack router set demo-router --external-gateway ext-net

# Create a few floating IPs
openstack floating ip create ext-net
openstack floating ip create ext-net
openstack floating ip create ext-net
openstack floating ip create ext-net
openstack floating ip create ext-net

# Add rules to the admin's security group to allow ping and ssh
SECURITY_GROUP=$(openstack security group list --project admin --format=csv | q -d , -H 'SELECT ID FROM -')
openstack security group rule create --ingress --protocol tcp --dst-port 22 ${SECURITY_GROUP}
openstack security group rule create --protocol icmp --ingress ${SECURITY_GROUP}
```

## Ajouter une clé ssh

```bash
openstack keypair create --public-key ~/.ssh/id_rsa.pub demo-keypair
```

## Créer un modèle

```bash
openstack flavor create --ram 2048 --disk 5 --vcpus 1 demo-flavor
openstack flavor create --ram 6144 --disk 20 --vcpus 2 cpu2-ram6-disk20
openstack flavor create --ram 12288 --disk 40 --vcpus 4 cpu4-ram12-disk40
```

## Démarrez une VM

```bash
#!/bin/sh

set -e
set -x

NETWORK_ID=$(openstack network list --name demo-net -c ID -f value)
IMAGE_ID=$(openstack image list -f csv 2>/dev/null | q -H -d , "SELECT ID FROM - WHERE Name LIKE 'debian-10%.qcow2'")
FLAVOR_ID=$(openstack flavor show demo-flavor -c id -f value)

openstack server create --image ${IMAGE_ID} --flavor ${FLAVOR_ID} \
	--key-name demo-keypair --nic net-id=${NETWORK_ID} --availability-zone nova:z-compute-1.example.com demo-server
```

## Ajouter la facturation

Le script ci-dessous attribuera une note de 0,01 à "`demo-flavour`":

```bash
cloudkitty module enable hashmap
cloudkitty module set priority hashmap 100
cloudkitty hashmap group create instance_uptime_flavor_id
GROUP_ID=$(cloudkitty hashmap group list -f value -c "Group ID")

cloudkitty hashmap service create instance
SERVICE_ID=$(cloudkitty hashmap service list -f value -c "Service ID")

cloudkitty hashmap field create ${SERVICE_ID} flavor_id
FIELD_ID=$(cloudkitty hashmap field list ${SERVICE_ID} -f value -c "Field ID")

FLAVOR_ID=$(openstack flavor show demo-flavor -f value -c id)

cloudkitty hashmap mapping create 0.01 --field-id ${FIELD_ID} --value ${FLAVOR_ID} -g ${GROUP_ID} -t flat
```

Le reste peut être trouvé ici: [https://docs.openstack.org/cloudkitty/latest/user/rating/hashmap.html](https://docs.openstack.org/cloudkitty/latest/user/rating/hashmap.html)

Ajoutez également le rôle rating à l'administrateur:

```plaintext
openstack role add --user admin --project admin rating
```

> Actuellement, après l'installation du cluster, tous les agents ceilometer doivent être redémarrés afin d'obtenir des métriques, même s'ils semblent bien configurés.
{.is-info}

## Ajouter le service Octavia

### Configuration scriptée

Tout ce qui est fait ci-dessous peut être fait avec 2 scripts d'aide :

```bash
oci-octavia-amphora-secgroups-sshkey-lbrole-and-network 
oci-octavia-certs
```

Tout d'abord, modifiez l'en-tête `/usr/bin/oci-octavia-amphora-secgroups-sshkey-lbrole-and-network`. Vous y trouverez ces valeurs :

```bash
# Set to either flat or vlan
OCTAVIA_NETWORK_TYPE=flat
# Set to the ID of the Octavia VLAN if the above is set to vlan
OCTAVIA_NETWORK_VLAN=876
# Set this to a value that matches something listed in /etc/neutron/plugins/ml2/ml2_conf.ini
# either in [ml2_type_flat]/flat_networks or in [ml2_type_vlan]/network_vlan_ranges
OCTAVIA_PHYSNET_NAME=external1

OCTAVIA_SUBNET_RANGE=192.168.104.0/24
OCTAVIA_SUBNET_START=192.168.104.4
OCTAVIA_SUBNET_END=192.168.104.250
OCTAVIA_SUBNET_GW=192.168.104.1
OCTAVIA_SUBNET_DNS1=84.16.67.69
OCTAVIA_SUBNET_DNS2=84.16.67.70
```

Modifiez-les à votre goût. Si vous utilisez vlan, la valeur de `OCTAVIA_NETWORK_TYPE` doit être `vlan` et la valeur de `OCTAVIA_PHYSNET_NAME` doit être "`external`". Les adresses IP décrites ci-dessus doivent être routables à partir des nœuds de contrôleur.

Une fois la modification terminée, exécutez le premier script, puis indiquez à OCI le groupe de sécurité et le démarrage réseau à utiliser comme ceci :

```bash
ocicli cluster-set CLUSTER_NAME --amp-secgroup-list SECGROUP_ID_1,SECGROUP_ID_2d5681bb2-044c-4de2-9f81-c3ca7d91abb6
ocicli cluster-set ver1 --amp-boot-network-list LOAD_BALANCER_NETWORK_ID
```

Ces identifiants peuvent être trouvés dans les journaux lors de l'exécution de `oci-octavia-amphora-secgroups-sshkey-lbrole-and-network`, ou dans `/etc/octavia/octavia.conf sous amp_secgroup_list` et `amp_boot_network_list`.

Maintenant, exécutez `oci-octavia-certs` sur l'un des contrôleurs, puis copiez `/etc/octavia/.ssh` et `/etc/octavia/certs` sur les autres contrôleurs.

```bash
rsync -e 'ssh -o UserKnownHostsFile=/dev/null -o StrictHostKeyChecking=no' -avz --delete /etc/octavia/certs/ root@z-controller-2:/etc/octavia/certs/
rsync -e 'ssh -o UserKnownHostsFile=/dev/null -o StrictHostKeyChecking=no' -avz --delete /etc/octavia/certs/ root@z-controller-3:/etc/octavia/certs/
rsync -e 'ssh -o UserKnownHostsFile=/dev/null -o StrictHostKeyChecking=no' -avz --delete /etc/octavia/.ssh/ root@z-controller-2:/etc/octavia/.ssh/
rsync -e 'ssh -o UserKnownHostsFile=/dev/null -o StrictHostKeyChecking=no' -avz --delete /etc/octavia/.ssh/ root@z-controller-3:/etc/octavia/.ssh/
```

Redémarrez `octavia-worker`, `octavia-health-manager` et `octavia-housekeeping`.

Ca y est, ça devrait marcher maintenant!

### Installation manuelle

Si vous souhaitez faire les choses manuellement, voici comment cela fonctionne.

Créez l'image Amphora. Cela peut être fait avec DIB (Disk Image Builder) comme ceci :

```bash
sudo apt-get install openstack-debianimages
/usr/share/doc/openstack-debian-images/examples/octavia/amphora-build
openstack image create --container-format bare --disk-format qcow2 --file debian-buster-octavia-amphora-2019.09.11-11.52-amd64.qcow2 --tag amphora debian-buster-octavia-amphora-2019.09.11-11.52-amd64.qcow2
```

Créez le réseau Octavia. Si, comme dans le package PoC, vous exécutez avec un pont `br-lb` spécifique lié à un réseau externe appelé `external1`, quelque chose comme ceci fera l'affaire :

```bash
openstack network create --external --provider-physical-network external1 --provider-network-type flat lb-mgmt-net
openstack subnet create --network lb-mgmt-net --allocation-pool start=192.168.104.4,end=192.168.104.250 --dns-nameserver 84.16.67.69 --dns-nameserver 84.16.67.70 --gateway 192.168.104.1 --subnet-range 192.168.104.0/24 lb-mgmt-subnet
```

L'exemple ci-dessus est lorsque vous n'utilisez pas `vlan`, mais que vous avez une carte réseau spécifique pour le réseau Octavia.

Ensuite, nous avons besoin de groupes de sécurité spécifiques pour Octavia :

> Assurez-vous d'utiliser `/root/octavia-openrc` et pas celui de l'administrateur

```bash
openstack security group create lb-mgmt-sec-grp
openstack security group rule create --protocol icmp lb-mgmt-sec-grp
openstack security group rule create --protocol tcp --dst-port 22 lb-mgmt-sec-grp
openstack security group rule create --protocol tcp --dst-port 9443 lb-mgmt-sec-grp
openstack security group rule create --protocol icmpv6 --ethertype IPv6 --remote-ip ::/0 lb-mgmt-sec-grp
openstack security group rule create --protocol tcp --dst-port 22 --ethertype IPv6 --remote-ip ::/0 lb-mgmt-sec-grp
openstack security group rule create --protocol tcp --dst-port 9443 --ethertype IPv6 --remote-ip ::/0 lb-mgmt-sec-grp

openstack security group create lb-health-mgr-sec-grp
openstack security group rule create --protocol udp --dst-port 5555 lb-health-mgr-sec-grp
openstack security group rule create --protocol udp --dst-port 5555 --ethertype IPv6 --remote-ip ::/0 lb-health-mgr-sec-grp
```

Ensuite, nous créons une paire de clés ssh :

```bash
mkdir /etc/octavia/.ssh
ssh-keygen -t rsa -f /etc/octavia/.ssh/octavia_ssh_key
chown -R octavia:octavia /etc/octavia/.ssh
rsync -e 'ssh -o UserKnownHostsFile=/dev/null -o StrictHostKeyChecking=no' -avz --delete /etc/octavia/.ssh/ root@z-controller-2:/etc/octavia/.ssh/
rsync -e 'ssh -o UserKnownHostsFile=/dev/null -o StrictHostKeyChecking=no' -avz --delete /etc/octavia/.ssh/ root@z-controller-3:/etc/octavia/.ssh/
. /root/octavia-openrc
openstack keypair create --public-key /etc/octavia/.ssh/octavia_ssh_key.pub octavia-ssh-key
```

Faites les certificats selon le tutoriel en amont à [https://docs.openstack.org/octavia/latest/admin/guides/certificates.html](https://docs.openstack.org/octavia/latest/admin/guides/certificates.html)

Resynchronisez les certificats sur les 2 autres contrôleurs :

```bash
rsync -e 'ssh -o UserKnownHostsFile=/dev/null -o StrictHostKeyChecking=no' -avz --delete /etc/octavia/certs/ root@z-controller-2:/etc/octavia/certs/
rsync -e 'ssh -o UserKnownHostsFile=/dev/null -o StrictHostKeyChecking=no' -avz --delete /etc/octavia/certs/ root@z-controller-3:/etc/octavia/certs/
```

Modifiez `octavia.conf` et définissez les ID `amp_boot_network_list` et `amp_secgroup_list`.

Redémarrez ensuite tous les services Octavia sur tous les contrôleurs.

Créez le rôle `load-balancer_admin` et attribuez-le :

```bash
openstack role create load-balancer_admin
openstack role add --project admin --user admin load-balancer_admin
```

Maintenant, il faut définir, avec ocicli, le réseau de démarrage et la liste des groupes de sécurité pour l'amphore :

```bash
ocicli cluster-set swift01 \
	--amp-boot-network-list 0c50875f-368a-4f43-802a-8350b330c127 \
	--amp-secgroup-list b94afddb-4fe1-4450-a1b8-25f36a354b7d,012584cd-ffde-483b-a55a-a1afba52bc20
```

Ensuite, nous pouvons commencer à utiliser Octavia :

```bash
openstack loadbalancer create --name lb-test-1 --vip-subnet-id ext-subnet
```

Comment utiliser l'équilibreur de charge est décrit ici: [https://docs.openstack.org/octavia/latest/user/guides/basic-cookbook.html](https://docs.openstack.org/octavia/latest/user/guides/basic-cookbook.html)

N'oubliez pas de créer le modèle :

```bash
openstack flavor create --ram 2048 --disk 4 --vcpus 2 --id 65 --private --project services octavia_65
```

### Utilisation d'Octavia comme équilibreur de charge HTTPS pour 2 serveurs Web

La documentation OpenStack contient tout ce dont vous avez besoin sur : [https://docs.openstack.org/octavia/latest/user/guides/basic-cookbook.html](https://docs.openstack.org/octavia/latest/user/guides/basic-cookbook.html)

Cependant, voici un exemple de création d'un équilibreur de charge avec un certificat HTTPS.

Création de l'équilibreur de charge pour le service "`foo`" :

```bash
openstack loadbalancer create \
    --name lb-foo \
    --vip-subnet-id pub01-subnet2
```

Créez le certificat et stockez-le dans Barbican. Tout d'abord, créez un certificat `x509` normal, avec les fichiers `key`, `crt` et `ca-chain`. Puis convertissez-le en un certificat `pkcs12` à l'aide de cette commande :

```bash
openssl pkcs12 -export -inkey server.key -in server.crt -certfile ca-chain.crt -passout pass: -out server.p12
```

Ensuite, nous le stockons dans Barbican, et conservons son adresse :

```bash
openstack secret store --name='tls_secret1' -t 'application/octet-stream' -e 'base64' --payload="$(base64 < server.p12)"
```

Création du `listener` :

```bash
openstack loadbalancer listener create \
    --name lb-foo-https \
    --protocol TERMINATED_HTTPS \
    --protocol-port 443 \
    --default-tls-container-ref https://z-api.example.com/keymanager/v1/secrets/e2e590a4-08b7-40e7-ab52-c06fd3a0a2dd \
    lb-foo
```

Création du `pool` :

```bash
openstack loadbalancer pool create \
    --name pool-foo-https \
    --protocol TERMINATED_HTTPS \
    --listener lb-foo-https \
    --lb-algorithm ROUND_ROBIN
```

Création des `membres du pool` :

```bash
openstack loadbalancer member create \
    --name foo-member-1-https \
    --address 10.4.42.10 \
    --protocol-port 443 \
    --subnet-id e499c943-09bb-46b7-8463-8d83ce51e830 \
    pool-foo-https
openstack loadbalancer member create \
    --name foo-member-2-https \
    --address 10.4.42.4 \
    --protocol-port 443 \
    --subnet-id e499c943-09bb-46b7-8463-8d83ce51e830 \
    pool-foo-https
```

## Configurer aucune limite pour les ressources de services

Comme certains services peuvent engendrer des instances, comme par exemple Octavia ou Magnum, il peut être souhaitable de ne définir aucune limite pour certaines ressources du projet de services :

```bash
openstack quota set --secgroup-rules -1 --secgroups -1 --instances -1 --ram -1 --cores -1 --ports -1 services
```

Le quota s'appliquera aux ressources virtuelles que le projet de services créera, par exemple, utilisez le quota d'équilibrage de charge `openstack show PROJECT_NAME` pour définir le nombre maximal d'équilibreur de charge pour un projet.

## Ajouter le service Magnum

Tout d'abord, téléchargez l'image coreos et définissez correctement les paramètres :

```bash
openstack image create --file coreos_production_openstack_image.img coreos_production_openstack_image.img
openstack image set --property os_distro=coreos coreos_production_openstack_image.img
```

Créez ensuite le modèle COE:

```bash
openstack coe cluster template create k8s-cluster-template \
    --image coreos_production_openstack_image.img --keypair demo-keypair \
    --external-network ext-net --dns-nameserver 84.16.67.69 --flavor demo-flavor \
    --docker-volume-size 5 --network-driver flannel --coe kubernetes
```

Puis créez le cluster Magnum:

```bash
openstack coe cluster create k8s-cluster \
                      --cluster-template k8s-cluster-template \
                      --master-count 1 \
                      --node-count 2
```

On dirait que les coreos ne fonctionneraient pas pour les k8. Au lieu :

```bash
wget https://download.fedoraproject.org/pub/alt/atomic/stable/Fedora-Atomic-27-20180419.0/CloudImages/x86_64/images/Fedora-Atomic-27-20180419.0.x86_64.qcow2
openstack image create \
                      --disk-format=qcow2 \
                      --container-format=bare \
                      --file=Fedora-Atomic-27-20180419.0.x86_64.qcow2 \
                      --property os_distro='fedora-atomic' \
                      fedora-atomic-latest
openstack coe cluster template create kubernetes-cluster-template \
	--image fedora-atomic-latest --keypair demo-keypair \
	--external-network ext-net --dns-nameserver 84.16.67.69 \
	--master-flavor demo-flavor --flavor demo-flavor \
	--docker-volume-size 5 --network-driver flannel \
	--coe kubernetes
```

# Liste de compatibilité matérielle

## Serveurs Dell

OCI a été testé avec ces types de serveurs PowerEdge:

-   DSS 2500
-   DSS 1500
-   DSS 1510
-   PowerEdge R410
-   PowerEdge R430
-   PowerEdge R440
-   PowerEdge R610
-   PowerEdge R620
-   PowerEdge R640
-   PowerEdge R720xd
-   PowerEdge R740xd
-   PowerEdge R6525 (processeurs AMD)

La prise en charge de racadm de Dell est incluse et OCI en fait un usage intensif.

## Serveurs HP

OCI a été testé avec ces types de serveurs Cloud Line (utilisés comme swiftstores):

-   CL2600 Gen10
-   CL2800 Gen10

Malheureusement, nous n'avons trouvé aucun moyen de configurer le BIOS de ces serveurs, donc un travail manuel doit être effectué pour configurer le BIOS manuellement, par exemple pour définir l'indicateur de connexion à chaud du disque dur. Cela peut être très ennuyeux lors de la configuration d'un grand nombre de serveurs.

OCI a également été testé avec ces serveurs (utilisés comme swiftstores):

-   ProLiant DL385 Gen10
-   ProLiant DL385 Gen10 Plus

OCI peut installer automatiquement `hponcfg`, `ssacli` et `storcli`, directement à partir du référentiel HP Debian. OCI utilise `hponcfg` pour activer automatiquement IPMI sur LAN (qui est désactivé par défaut sur ces serveurs).

# Mise à jour

## De stretch-rocky à buster-rocky

### Mise à niveau des nœuds de calcul

Commencez par basculer `apt/sources.list` sur `buster` et supprimez les dépôts de `backport Ceph` en amont. Ensuite, supprimez toutes les traces de Ceph du flux amont :

```bash
apt-get purge libcephfs2 librados2 librbd1 python3-rgw python3-rbd python3-rados python3-cephfs librgw2
```

Cela supprimera probablement certains composants Nova, faites-le quand même. Ensuite, effectuez la mise à niveau dist. Appuyez simplement sur l'entrée sur n'importe quelle invite, ou exécutez en mode non interactif pour les invites Debconf. Ensuite, lancez simplement Puppet.

### Mise à niveau des nœuds de volume

Rien de spécial ici, il suffit de les mettre à niveau avec `apt`, de redémarrer et d'appliquer puppet. Il peut être bien sûr souhaitable de migrer en direct les volumes avant de redémarrer.

### Mise à niveau de vos contrôleurs

La mise à niveau des contrôleurs de Stretch vers Buster n'est pas une tâche facile, OCI inclut donc un script pour automatiser la tâche :

```bash
oci-cluster-upgrade-stretch-to-buster CLUSTER_NAME
```

Ça va tout faire pour toi. Il est fortement conseillé de tester ceci avant de le faire sur un cluster live. La mise à niveau prend environ 1 heure si elle est exécutée avec 3 contrôleurs.

## Mise à niveau d'une version d'OpenStack à la suivante

OCI est livré avec un script shell qui vous aide à effectuer les mises à niveau d'OpenStack de manière entièrement automatisée :

```bash
oci-cluster-upgrade-openstack-release CLUSTER_NAME FROM TO
```

Par exemple, si vous souhaitez mettre à niveau votre cluster nommé "`cl1`" de Rocky vers Stein, faites simplement :

```bash
oci-cluster-upgrade-openstack-release cl1 rocky stein
```

Notez que vous ne pouvez pas ignorer la version d'OpenStack. Si vous souhaitez passer de Rocky à Victoria, vous devez faire :

```bash
oci-cluster-upgrade-openstack-release cl1 rocky stein
oci-cluster-upgrade-openstack-release cl1 stein train
oci-cluster-upgrade-openstack-release cl1 train ussuri
oci-cluster-upgrade-openstack-release cl1 ussuri victoria
```

Notez qu'après la mise à niveau vers buster-victoria, vous devez ensuite mettre à niveau votre cluster vers Bullseye de la manière décrite ci-dessus (en gardant toujours Victoria), et j'espère que vous pourrez passer à Wallby :

```bash
oci-cluster-upgrade-openstack-release cl1 victoria wallby
```