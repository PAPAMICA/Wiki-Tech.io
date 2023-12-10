---
title: Feuille de route pour site WordPress
description: 
published: true
date: 2023-12-10T12:58:28.449Z
tags: wordpress
editor: markdown
dateCreated: 2023-12-10T11:30:59.206Z
---

# Automatisation
### Installer tous les plugins recommandés

# Déploiement de WordPress
## Portainer
## Uploads.ini
Ce fichier permet d'augmenter les limites pour l'envoie de fichier à WordPress :
```ini
file_uploads = On

memory_limit = 500M

upload_max_filesize = 500M

post_max_size = 500M

max_execution_time = 600
```
# Sauvegarde
## UpdraftPlus
- Installer et activer l'extension **UpdraftPlus**
- Connecter le compte Google Drive dans l'onglet **Réglages**
- Configurer les sauvegardes automatiques dans l'onglet **Réglages**

# Sécurisation
## Mise à jours automatique
- Configurer les mises à jours automatique pour l'ensemble des extensions
## Wordfence Security
- Installer et activer l'extension **Wordfence Security**
- Activer les scans automatiques
- Activer le firewall
- Activer la 2FA pour l'ensemble des comptes
## WP Hide & Security Enhancer
- Installer et activer l'extension **WP Hide & Security Enhancer**
- Lancer le setup d'exemple
- Activer le captcha v3 dans l'onglet **WP Hide → Security → Captcha**
- Changer le lien d'administration dans l'onglet **WP Hide → Hide → Login/Admin**
## WP Mail SMTP
- Installer et activer l'extension **WP Mail SMTP**
- Configurer le serveur SMTP dans l'onglet **WP Mail SMTP → Réglages**

> ### Configuration pour PAPAMICA :
> - Adresse email d'envoi : **notification@domain.com**
> - Forcer l'e-mail d'expédition : **Activé**
> - Nom de l'expéditeur : **Nom_du_site**
> - Forcer le nom d'expéditeur : **Activé**
> - Service d'envoi : **Autre SMTP**
> - Hébergeur SMTP : **10.20.10.201**
> - Cryptage : **Aucun**
> - Port SMTP : **25**
> - TLS auto : **Désactivé**
> - Authentification : **Désactivé**
{.is-info}


# Supervision
## UptimeKuma
### Monitor HTTPS - Keyword
Je conseil fortement l'utilisation d'une sonde de type **HTTPS - Keyword** pour être alerté si le site répond, mais répond une page blanche par exemple.
- Créer la sonde sur un mot qui apparait sur la page d'accueil
- L'opération peut être répétée pour l'ensemble des pages
- Penser à activer le vérification du certificat
- Penser à configurer les alertes
## Zabbix
Deux projets existent pour ajouter de la supervision WordPress via Zabbix :
- https://github.com/WapplerSystems/wp-zabbix
- https://github.com/milzulempis/Zabbix-Wordpress-Template
## JetPack
- Installer et activer l'exension **Jetpack**
- Activer la **Surveillance des temps d’arrêt** dans l'onglet **JetPack → Réglages**
- Lier Jetpack avec compte WordPress.com

# Mise en page du site
## Elementor
- Installer et activer l'extension **Elementor**
## Elementor Pro
- Télécharger le packet **Elementor Pro** sur le site my.elementor.com
- Installer et activer l'extention **Elementor Pro**
## Envato Elements
## Duplicate Page


# Direction artistique
## Thème général du site
Tous les paramètres se trouvent dans ce menu :
**Modification avec Elementor → Menu 3 traits en haut à gauche → Réglages du site**
- Couleurs
- Police d'écriture
- Favicon
## Elements de navigation
## Entête et menu
- Logo
- Menu ordinateur
- Menu téléphone
- Call to Action
## Pied de page
- Logo
- Réseaux sociaux
- Liens utiles
- Contact / Newsletter
- Copyright

# Création des pages templates
## Page de blog
Permet de faire des articles ou un portfolio.
- Faire la page principale d'affichage
- Faire le template des articles
## Page Erreur 404

# Pages obligatoires légalement
Quelques liens utiles :
- https://www.economie.gouv.fr/entreprises/site-internet-mentions-obligatoires
- https://entreprendre.service-public.fr/vosdroits/F31228
- https://www.francenum.gouv.fr/guides-et-conseils/developpement-commercial/site-web/quelles-sont-les-mentions-legales-pour-un-site

## Page Mentions légales
Exemple de page mentions légales :
```markdown
# Édition Du Site
<URL_du_site> est un site édité par la société Genevois Informatique (Mickael Asseline EI) pour la cliente <Nom_client>

Siège : 11 Chemin du Crêt Millet, 74160 Saint Julien en Genevois
Mail : contact@genevois-informatique.com

# Propriété Intellectuelle
L’ensemble de ce site relève de la législation française et internationale sur le droit d’auteur et la propriété intellectuelle. Tous les droits de reproduction sont réservés, y compris les représentations iconographiques et photographiques. La reproduction, adaptation et/ou traduction de tout ou partie de ce site sur un support quel qu’il soit, est formellement interdite sauf autorisation expresse du Directeur de la publication.

# Modification Du Site
L’équipe éditoriale se réserve le droit de modifier ou de corriger le contenu de ce site et de ces mentions légales à tout moment et ceci sans préavis.

# Hébergeur
Le site <URL_du_site> est hébergé par la société Genevois Informatique (Mickael Asseline EI) sur l’infrastructure Public Cloud d’Infomaniak.

**Infomaniak Network SA**
Siège : Rue Eugène Marziano 25, 1227 Les Acacias (GE) (Suisse)
Registre du commerce du canton de Genève : CH-660.0.059.996-1
Numéro IDE : CHE-103.167.648
Numéro TVA : CHE-103.167.648
```

## Page CGV
Exemple de CGV : https://creation-site-internet.pagesjaunes.fr/ebibliotheque/voir/254091/cgv-d-un-site-internet

# Contact
## Elementor
## Contact Form 7
## Contact Form CFDB7
## Captcha

# Analytiques
## Cookies GPRD
## Google Analytics
## JetPack
## Matomo
## Umami
## Autre code JS


# Optimisation
## Pagespeed
## WP-Optimize
## Smush

# SEO
## Yoast SEO

# Maintenance
## Contrat de maintenance
## Mettre des rappels
## Ce qui est à vérifier
