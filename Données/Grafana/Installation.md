---
title: Installation
description: Mise en place de Grafana sous Docker !
published: true
date: 2021-06-14T08:14:49.941Z
tags: 
editor: markdown
dateCreated: 2021-05-24T10:35:06.214Z
---

# Présentation

# Installation

# Configuration

## Ajouter une base de données

## Connexion avec Keycloak

```yaml
- GF_AUTH_GENERIC_OAUTH_ENABLED=true
- GF_AUTH_GENERIC_OAUTH_SCOPES=email
- GF_AUTH_GENERIC_OAUTH_NAME=<NAME>
- GF_AUTH_GENERIC_OAUTH_ALLOW_SIGN_UP=true
- GF_AUTH_GENERIC_OAUTH_CLIENT_ID=grafana
- GF_AUTH_GENERIC_OAUTH_CLIENT_SECRET=<CLIENT_SECRET>
- GF_AUTH_GENERIC_OAUTH_AUTH_URL=https://<URL>/auth/realms/<REALMS>/protocol/openid-connect/auth
- GF_AUTH_GENERIC_OAUTH_TOKEN_URL=https://<URL>/auth/realms/<REALMS>/protocol/openid-connect/token
- GF_AUTH_GENERIC_OAUTH_API_URL=https://<URL>/auth/realms/<REALMS>/protocol/openid-connect/userinfo
- GF_AUTH_GENERIC_OAUTH_ROLE_ATTRIBUTE_PATH=contains(roles[*], 'admin') && 'Admin' || contains(roles[*], 'editor') && 'Editor' || 'Viewer'
```