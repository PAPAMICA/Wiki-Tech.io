---
title: Keycloak
description: Centralisation de l’authentification
published: true
date: 2021-06-14T06:40:15.635Z
tags: 
editor: markdown
dateCreated: 2021-05-24T10:34:51.230Z
---

# Présentation

# Installation

# Configuration

## Portainer

![](/images/image_2021-05-03_212514.png)

## Grafana

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

## WikiJS