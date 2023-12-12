---
title: Feuille de route pour site WordPress
description: 
published: true
date: 2023-12-12T14:08:13.623Z
tags: wordpress
editor: markdown
dateCreated: 2023-12-10T11:30:59.206Z
---

# Automatisation
### Installer tous les plugins recommandés
Se connecter au container WordPress et lancer cette commande :
```bash
bash -c "$(curl -s https://raw.githubusercontent.com/PAPAMICA/SuperWordPress/main/manual_install.sh)"
```

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

## Politique de Confidentialité
Générateur en ligne : https://la-webeuse.com/generateur-politique-de-confidentialite-wordpress/

Exemple de page Politique de Confidentialité : https://genevois-informatique.com/politique-de-confidentialite-2/


## Page Mentions légales
Générateur de mentions légales pour WordPress : https://la-webeuse.com/generateur-mentions-legales#lwptoc15

Exemple de page mentions légales : https://genevois-informatique.com/mentions-legales/


## Page CGV
Exemple de CGV : https://creation-site-internet.pagesjaunes.fr/ebibliotheque/voir/254091/cgv-d-un-site-internet

# Contact
## Elementor Pro
Le plugin Elementor permet la création de formulaire et tout sa gestion.
## Contact Form 7
Autre solution, le plugin Contact Form 7
## Contact Form CFDB7
Ce plugin permet de garder un historique de tous les formulaires remplis directement sur le site.
## Captcha
Il est indispensable de configurer le Captcha v3 sur tous les formulaires.
Sa configuration peux se faire directement dans 

# Analytiques
## Complianz RGPD
## Burst Statistics


# Optimisation
## Pagespeed
- Tester votre site avec https://pagespeed.web.dev/
## Lite speed cache
- Installer et activer l'extension **Light speed cache**
- Appliquer les préréglages avancés
- Configuré la clé d'API QUIC
## Redis
- Pour cela il faut un serveur Redis 
```yaml
version: "2"
services:
  redis:
    image: 'redis:alpine'
    restart: always
    container_name: redis
    expose:
      - '6379'
    networks:
      - default
networks:
  default:
    external:
      name: proxy
```
- Ajouter les variables suivantes dans 'wp-config.php' :
```php
define('WP_REDIS_HOST', 'redis'); # Utiliser le nom du container
define('WP_REDIS_PORT', '6379'); # Utiliser le port du container
define('WP_REDIS_DATABASE', '0'); # Utiliser un Id de base de données par site (0-16)

```
- Installer et activer l'extension **Redis Object Cache**
- Vérifier la configuration

# SEO
## Yoast SEO

# Maintenance
## Contrat de maintenance
## Mettre des rappels
- Prévoir 30 min chaque mois par site.
## Ce qui est à vérifier
- Mise à jour
- Affichage du site
- Score PageSpeed
- Supervision
- SEO
