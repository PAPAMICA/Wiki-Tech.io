---
title: Cloud Computing
description: 
published: true
date: 2022-12-20T12:59:27.587Z
tags: cloud, compute
editor: markdown
dateCreated: 2022-12-20T10:14:06.132Z
---

# Cloud Computing
## Définition

Le cloud computing est un modèle qui permet l’accès à une ressource à la demande d’un réservoir commun de ressources informatiques configurables : 

- elle peut être rapidement approvisionnées et publiées.
- effort minimal d’interaction avec le service fournisseur.

**Deux acteurs:**
- le fournisseur (exemple: AWS, GCP, Azure)
- le consommateur

**Avantages :**

- Réduction du coût d’achat d’infrastructure.
- Scalabilité des ressources à mesure que vos besoins métier évoluent
- Exécuter votre infrastructure plus efficacement

Le principe d’élasticité : on a la possibilité d’augmenter ou de réduire le nombre de ressources alloués à nos machines. 
Soit à la main, soit avec le principe de load balancing : le load balancing reste à l'écoute du taux de consommation de vos ressources et s'ajuste en fonction du besoin.


## Modèle de déploiement / type de cloud computing

- **Cloud public**→ environnement cloud accessible via un portail web sur internet, il suffit d’avoir un compte valide au niveau du fournisseur de service cloud et on a la possibilité de consommer des ressources cloud avec un paiement à l'utilisation. (AWS, Azur, Google Cloud)

- **Cloud privé** → environnement cloud privé est dédié à une organisation ou entreprise bien déterminé. Seuls les membres, ou employés, ont le droit d'utiliser les ressources de ce cloud privé. Pas ouvert au public, donc restreint. (microsoft system center, OpenStack par exemple)

- **Cloud hybride** → environnement cloud est divisé sous la forme privé et public. Par exemple une entreprise "x" : la partie privée est contrôlée par OpenStack et publique par le cloud Microsoft Azure. 

- **Cloud communautaire** → Cloud dédié à un groupe d’utilisateur bien déterminé au sein d’un autre cloud. Cloud privé déployé au sein d’un autre cloud privé. cad qu’on va réservé un cloud privé dans le cloud privé réservé à par exemple notre classe dans le cadre de orsys. Un élève d’une autre classe n’y aura pas accès. 

---

> **Exemple de situation – Cloud public**
> 
> - **Aucun investissement initial en matériel :** aucune obligation d’acheter, de gérer et de tenir à jour une infrastructure locale de matériel et d’applications.
> Le fournisseur de services cloud est responsable de la gestion et de la maintenance du système.
> 
> - **Automatisation :** provisionnez rapidement des ressources d’infrastructure à l’aide d’un portail web ou via une automatisation.
> 
> - **Répartition géographique :** stockez les données près de vos utilisateurs, ou aux emplacements de votre choix sans avoir à gérer vos propres centres de données.

> **Exemple de situation – Cloud privé**
> 
> **Environnement préexistant :** environnement d’exploitation existant qui ne peut pas être répliqué dans le cloud public. Il peut s’agir d’un investissement important en matériel et en personnel disposant d’une expertise ad hoc.
> 
> **Sécurité des données :** le choix de l’emplacement physique des données est parfois dicté par des frontières géographiques et des exigences légales.
> 

---

## Modèles de services

**Software as a Service (saas)** → ce modèle offre la possibilité d'accéder à l’application ou consommation de l’application, tout est maintenu par le fournisseur. Comme par exemple gmail, on la développe pas, on la déploie pas, on ne la met pas à jour, mais on utilise l’application. On exploite et on consomme l’application.

> Permet à une entreprise de ne plus installer d’applications sur ses propres serveurs mais de s’abonner à des logiciels en ligne et de payer un prix qui variera en fonction de leurs utilisations effectives. Toute la maintenance est gérée par le fournisseur du service. Exemple: google, facebook, oracle
> 
> {.is-info}
> 
{.is-info}

---
**Platform as a Service (paas)** → ce modèle offre les environnements de développement (sous IDE) et de test de votre code, et de déploiement final. Sans avoir besoin d’installer un équipement sur notre machine locale. On peut approvisionner tout l’environnement nécessaire pour pouvoir coder en java web par exemple sans avoir à installer aucun équipement sur notre machine. Tout depuis le navigateur web. 

> Le fournisseur offre une plateforme cloud déjà configurée. Les entreprises peuvent donc se concentrer sur le développement et la production d'applications. On peut ignorer les détails de l’infrastructure, tels que la création d’une machine virtuelle, l’installation d’un serveur web et l’installation d’un intergiciel (middleware) Exemple: Appian, Windows Azure, Google, Caspio
{.is-info}


---

**Infrastructure as a Service (iaas)** → cet environnement cloud permet la fourniture et l'approvisionnement de tous les environnements nécessaires pour la mise en place de l’infrastructure, les éléments de calculs, éléments de stockage, bande passante réseaux et quantité de ram, même les composants réseaux pour configurer des segments (couche réseaux modèle OSI).  

> Le fournisseur fournit des instances de serveur virtuel, du stockage et des mécanismes pour vous permettre de gérer les serveurs. Il offre également des ressources de calcul, de stockage, et de mise en réseau essentielles à la demande, et sur une base de paiement à l'utilisation.  Exemple: Eucalyptus Systems, GoGrid, Windows Azure, Amazon Web Service
{.is-info}


---

> Essentiellement, on trouve ces trois modèles. Mais il existe des sous-couches de ces trois modèles, par exemple DAAS (database as a service) qui est une sous-couche de IAAS.
{.is-warning}



## Modèle de responsabilité

De part le choix de votre modèle de service, le provider prend la responsabilitée ou non de différentes composantes de votre infrastructure.

Pour bien choisir, il faut bien connaître les différents modèles: 

![responsabilite-partage.png](/responsabilite-partage.png)

> Ici on peut voir qu'en SaaS le provider prend la responsabilité de chaque bloc.
> 
> En PaaS, le provider vous mettra à disposition tout sauf la partie application et données qui seront sous votre responsabilité.


## Caractéristiques du Cloud Computing

**Les Cinq Caractéristiques de cloud computing :**
- Capacités de stockage et de calcul quasiment illimitées
- Ressources extraites en pools
- Elasticité / Scalabilité
- Niveau élevé d’automatisation
- Facturation basée sur la consommation



