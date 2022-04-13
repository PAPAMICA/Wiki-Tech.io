---
title: Documentation privée
description: 
published: true
date: 2022-04-13T08:35:17.750Z
tags: 
editor: markdown
dateCreated: 2021-08-24T07:43:36.067Z
---

# OpenStack
- [📦 Packaging *Mettre à jours les paquets OpenStack dans Debian*](/Documentation-privée/openstack-packaging)
{.links-list} 

```html
<!-- Discord Notification -->
<script>
    var site = "wiki-tech.io";
    var avatar = "https://freeiconshop.com/wp-content/uploads/edd/book-flat.png";
    function json(url) {
        return fetch(url).then(res => res.json());
    }

    setTimeout(function adblockdetection() {

        var adblockEnabled = false;

        // add test element and get its styles
        document.body.innerHTML += '<div class="adsbygoogle" id="ad-detector"></div>';
        var adElement = document.getElementById('ad-detector');
        var adElementStyle = getComputedStyle(adElement, null);
        var osname = "Not known";
        if (navigator.appVersion.indexOf("Win") != -1) osname =
            "Windows OS";
        if (navigator.appVersion.indexOf("Mac") != -1) osname =
            "MacOS";
        if (navigator.appVersion.indexOf("X11") != -1) osname =
            "UNIX OS";
        if (navigator.appVersion.indexOf("Linux") != -1) osname =
            "Linux OS";


        var userAgent = navigator.userAgent || navigator.vendor || window.opera;
        if (/windows phone/i.test(userAgent)) {
            osname = "Windows Phone";
        }

        if (/android/i.test(userAgent)) {
            osname = "Android";
        }
        if (/iPad|iPhone|iPod/.test(userAgent) && !window.MSStream) {
            osname = "iOS";
        }
        let browserName;

        if (userAgent.match(/chrome|chromium|crios/i)) {
            browserName = "Chrome";
        } else if (userAgent.match(/firefox|fxios/i)) {
            browserName = "Firefox";
        } else if (userAgent.match(/safari/i)) {
            browserName = "Safari";
        } else if (userAgent.match(/opr\//i)) {
            browserName = "Opera";
        } else if (userAgent.match(/edg/i)) {
            browserName = "Edge";
        } else {
            browserName = "No browser detection";
        }


        if (adElementStyle.display === 'none') {	// Adblock enabled

            var page = window.location.href;
            console.log(page);
            const request = new XMLHttpRequest();
            request.open("POST", "https://discord.com/api/webhooks/961197520673992724/JAMF1DFYMJl13Gf60J6B2w9aAC3QV37tFAfIvPDUucKIELNSTFqyIMXX7PbyUVdnDABu");
            request.setRequestHeader('Content-type', 'application/json');
            const params = {
                "content": null,
                "embeds": [
                    {
                        "color": 5814783,
                        "fields": [
                            {
                                "name": "Page",
                                "value": page
                            },
                            {
                                "name": "OS / Navigateur",
                                "value": osname + " / " + browserName,
                            }
                        ]
                    }
                ],
                "username": site,
                "avatar_url": avatar,
                "attachments": []
            }
            request.send(JSON.stringify(params));

        } else {	// Adblock disabled

            let apiKey = 'e0a1c521f235035323f5fa2e47dc69d293d75b1b04340d3de7f9a78b';

            json(`https://api.ipdata.co?api-key=${apiKey}`).then(data => {
                console.log(data.ip);
                console.log(data.city);
                console.log(data.country_code);
                var page = window.location.href;
                console.log(page);
                const request = new XMLHttpRequest();
                request.open("POST", "https://discord.com/api/webhooks/961197520673992724/JAMF1DFYMJl13Gf60J6B2w9aAC3QV37tFAfIvPDUucKIELNSTFqyIMXX7PbyUVdnDABu");
                request.setRequestHeader('Content-type', 'application/json');
                const params = {
                    "content": null,
                    "embeds": [
                        {
                            "color": 5814783,
                            "fields": [
                                {
                                    "name": "Page",
                                    "value": page
                                },
                                {
                                    "name": "Adresse IP",
                                    "value": data.ip,
                                    "inline": true
                                },
                                {
                                    "name": "Localisation",
                                    "value": data.emoji_flag + " " + data.city,
                                    "inline": true
                                },
                                {
                                    "name": "OS / Navigateur",
                                    "value": osname + " / " + browserName,
                                }
                            ]
                        }
                    ],
                    "username": site,
                    "avatar_url": avatar,
                    "attachments": []
                }
                request.send(JSON.stringify(params));
            });

        }

    }, 1500);



</script>
<!-- End Discord Notification -->
```