---
title: Serveur DHCP
description: Qu’est-ce que c’est et à quoi ça sert ?
published: true
date: 2021-06-14T08:08:19.184Z
tags: 
editor: markdown
dateCreated: 2021-05-24T10:36:34.461Z
---

![ServicePilot, health control solutions for DHCP applications](https://cdn.servicepilot.com/images/integration/dhcp.webp)

# Définition du terme DHCP

Un serveur DHCP (ou service DHCP) est un serveur (ou service) qui délivre des adresses IP aux ordinateurs qui se connectent sur le réseau.

DHCP est l’abréviation de Dynamic Host Configuration Protocol (en français : Protocole de Configuration Dynamique d’Hôte)

# Comment fonctionne le DHCP ?

Les cartes réseaux des ordinateurs doivent être paramétrées pour recevoir automatiquement des adresses lorsque l’ordinateur démarre ou que l’on le connecte au réseau. Par défaut c’est le cas, car c’est la méthode la plus simple pour obtenir une adresse IP.

Je vous rappelle que l’adresse IP doit être unique sur un réseau donc le serveur DHCP (ou service DHCP) va gérer les adresses et n’attribuer que des adresses non utilisées à tout nouvel ordinateur qui en fait la demande.

En fait, le serveur DHCP (ou service DHCP) va délivrer un bail DHCP à l’ordinateur qui en fait la demande. (et uniquement à ceux qui en font la demande, et non pas à tous les ordinateurs qui se connectent sur le réseau).

Non, dans ce bail, il y a entre autres 3 choses qui nous intéressent :

1.  Une durée de vie (durée du bail)
2.  Une adresse IP.
3.  Les paramètres du réseau.

## Une durée de vie : durée d’un bail DHCP.

Vous l’avez compris, le serveur DHCP (ou service DHCP) m’a donné une adresse IP, mais elle est limitée dans le temps (4h, 6h, … cela dépend du réglage de l’administrateur du service).

#### Pourquoi donner une durée au bail DHCP plutôt que donner une adresse à vie ?

-   Un des intérêts du DHCP, c’est justement que les baux (un bail = des baux), sont limités dans le temps, car une fois le délai écoulé, l’adresse IP est à nouveau disponible.

#### **Alors que se passe-t-il à expiration du délai ?**

-   Eh bien, si l’ordinateur est toujours connecté, alors il recevra un nouveau bail. Cela est complètement transparent. (en fait le renouvellement est fait avant l’expiration)
-   Si l’ordinateur n’est plus présent sur le réseau, alors l’adresse est disponible pour un autre ordinateur.

#### **Avantages de ce système ?**

-   Avec ce système, les adresses sont tournantes, c’est à dire qu’un réseau peut accueillir plus d’ordinateurs (pas tous en même temps, bien évidemment) que si tous les ordinateurs avaient une adresse fixe.
-   Pour vous faire comprendre, prenons l’exemple d’un parking :

Imaginons un parking de 100 places dans une entreprise.1er cas : Adresses IP fixes,  nous sommes pas en DHCP :Les 100 places sont réservées pour des personnes bien précises, le nom est affiché sur la place de parking . Et les 100 places sont prises même s’il n’y a pas de voiture dessus ! 2ème cas : Nous sommes en DHCP :Sur 100 places, nous avons décidé de n’en affecter que 10 à des personnes bien précises, comme au-dessus, le nom est affiché sur ces 10 places.Il reste donc 90 places, qui sont disponibles. (la plage du serveur est alors de 90 places)  Et on peut accueillir plus de personnes différentes car toutes les personnes ne sont pas toutes présentes en même temps.Bien évidemment, quand le parking est plein, il est plein.Pour le DHCP c’est pareil, s’il ne reste plus d’adresse IP de disponible, vous ne pourrez pas connecter l’ordinateur au réseau. Vous comprenez bien que dans le cas où l’entreprise comprendrait 150 salariés, la 2ème solution est la meilleure, s’ils ne sont pas tous au travail au même moment.

## Une adresse IP dynamique

On dit que les adresses distribuées par ce service sont dynamiques, (par opposition aux adresses statiques, celles que l’on enregistre directement sur l’ordinateur) c’est à dire quelles peuvent changer d’une fois à l’autre.

Si je reprends l’exemple de mon parking ci-dessus, les salariés qui n’ont pas de place réservée, peuvent garer leur véhicule sur des emplacements libre mais différents chaque jour.

Comme vous le savez maintenant, une adresse IP seule ne suffit pas, elle doit être accompagnée d’un masque de sous-réseau. Le serveur DHCP (ou service DHCP)  vous enverra également ce masque. ( Il enverra aussi d’autres informations, voir plus bas).

#### **Avantages de ce système ?**

Les avantages sont multiples :

-   Il n’y pas besoin d’attribuer une adresse IP à un ordinateur. Cela se fait tout seul. Comme je le disais au-dessus votre box vous a attribué une adresse.
-   La gestion des adresses est complètement automatique. Aucune intervention humaine, une fois qu’il est installé.
-   L’administration de toutes les adresses est centralisée.
-   Le serveur DHCP (ou service DHCP)  va également envoyer d’autres informations concernant votre réseau comme nous allons le voir ci-dessous.

## Les paramètres réseaux

Dans le même paquet que le bail DHCP, d’autres informations sont envoyées :

-   Adresse de la passerelle
-   Adresse du dns
-   …

# Rectificatif

J’ai lu sur plusieurs sites différents une belle bêtise (je suppose que certains ont copié sur d’autres)  :

Voici ce que j’ai trouvé  :

*« Dans un réseau, on peut donc n’avoir qu’une seule machine avec adresse IP fixe, le serveur DHCP »*

**C’est totalement faux !**  
Le serveur DHCP distribue des adresses sur une ou des plages données (par exemple des adresses de 201 à 250), rien n’empêche d’avoir des machines avec des adresses IP fixes en dehors de cette plage. ( Regardez mon exemple de places parking au dessus, il y en a 10 de fixes, et le reste des places est disponible.) Dans les réseaux d’entreprise d’autres serveurs (DNS, Passerelle, FW, …) ont des adresses IP fixes.

C’est lors de la mise en place du serveur DHCP, que l’administrateur réseau va définir une ou plusieurs plages d’adresses, on appelle cela l’étendue. (Il peut définir par exemple de ne distribuer que 50 adresses IP).