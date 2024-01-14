---
layout: post
title:  "Uptime Kuma (Docker)"
date: 2024-01-14 10:00:00 -600
categories: [Monitoring]
tags: [monitoring,docker]
toc: true
---

## Docker Compose File

Here is what the docker-compose.yml file should look like:

```yaml
version: '3'
services:
  uptime-kuma:
    image: louislam/uptime-kuma:latest
    container_name: uptime-kuma
    volumes:
      - ./uptime-kuma-data:/app/data
    ports:
      - 3001:3001  # <Host Port>:<Container Port>
    restart: always
```

Here is the Uptime Kuma GitHub:
https://github.com/louislam/uptime-kuma

### Docker One-Line Command

```shell
docker run -d --restart=always -p 3001:3001 -v uptime-kuma:/app/data --name uptime-kuma louislam/uptime-kuma:1
```

This will run it locally on localhost:3001

### Reset Password

```shell
docker exec -it uptime-kuma npm run reset-password
```