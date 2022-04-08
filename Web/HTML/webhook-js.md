---
title: Webhook JS
description: Envoyer un webhook avec du javascript
published: true
date: 2022-04-08T12:38:52.159Z
tags: wordpress, js, discord, webhook, website
editor: markdown
dateCreated: 2022-04-08T12:38:52.159Z
---

# Webhook en javascript
Le script s'executera à chaque rafraichissement de la page.
Il suffit d'ajouter ce petit bout de code dans le header de votre site :

```js
<!-- Discord Notification -->
<script>
    const request = new XMLHttpRequest();
    request.open("POST", "<webhook_url>");
    request.setRequestHeader('Content-type', 'application/json');
    const params = {
    username: "<username>",
    avatar_url: "<avatar_url>",
    content: "<content>"
    }
    request.send(JSON.stringify(params));
  </script>
<!-- End Discord Notification -->
```
> Pensez à modifier les valeurs *webhook_url*, *username*, *avatar_url* et *content*.
{.is-warning}


Vous pouvez utiliser [cette extension](https://wordpress.org/plugins/insert-headers-and-footers/) pour intégrer ce code à votre site WordPress.