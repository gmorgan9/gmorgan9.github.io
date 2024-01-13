---
layout: post
title:  "Set up a Website CloudFlare Tunnel (Linux)"
date: 2024-01-13 17:00:00 -500
categories: [Web Devlopment]
tags: [cloudflare,linux,website]
author: garrett
toc: true
---

# Service Side Steps

## Step 1
You are going to need to create a directory in you /var/www directory connected with apache2. Do this by using this command, where you will change the domain.com with your desired domain/subdomain name:

```
sudo mkdir -p /var/www/domain.com/public_html
```
