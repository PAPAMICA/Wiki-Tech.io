---
title: Cloud Computing
description: 
published: true
date: 2022-12-20T10:32:54.003Z
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

- Cloud public → environnement cloud accessible via un portail web sur internet, il suffit d’avoir un compte valide au niveau du fournisseur de service cloud et on a la possibilité de consommer des ressources cloud avec un paiement à l'utilisation. (AWS, Azur, Google Cloud)

- Cloud privé → environnement cloud privé est dédié à une organisation ou entreprise bien déterminé. Seuls les membres, ou employés, ont le droit d'utiliser les ressources de ce cloud privé. Pas ouvert au public, donc restreint. (microsoft system center, OpenStack par exemple)

- Cloud hybride → environnement cloud est divisé sous la forme privé et public. Par exemple une entreprise "x" : la partie privée est contrôlée par OpenStack et publique par le cloud Microsoft Azure. 

- Cloud communautaire → Cloud dédié à un groupe d’utilisateur bien déterminé au sein d’un autre cloud. Cloud privé déployé au sein d’un autre cloud privé. cad qu’on va réservé un cloud privé dans le cloud privé réservé à par exemple notre classe dans le cadre de orsys. Un élève d’une autre classe n’y aura pas accès. 

---

**Exemple de situation – Cloud public**

- **Aucun investissement initial en matériel :** aucune obligation d’acheter, de gérer et de tenir à jour une infrastructure locale de matériel et d’applications.
Le fournisseur de services cloud est responsable de la gestion et de la maintenance du système.

- Automatisation : provisionnez rapidement des ressources d’infrastructure à l’aide d’un portail web ou via une automatisation.

- Répartition géographique : stockez les données près de vos utilisateurs, ou aux emplacements de votre choix sans avoir à gérer vos propres centres de données.

**Exemple de situation – Cloud privé**

Environnement préexistant : environnement d’exploitation existant qui ne peut pas être répliqué dans le cloud public. Il peut s’agir d’un investissement important en matériel et en personnel disposant d’une expertise ad hoc.

Sécurité des données : le choix de l’emplacement physique des données est parfois dicté par des frontières géographiques et des exigences légales.


---









