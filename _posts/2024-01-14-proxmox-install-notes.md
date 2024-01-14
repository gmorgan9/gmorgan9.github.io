---
layout: post
title:  "Proxmox Install Notes"
date: 2024-01-14 10:00:00 -500
categories: [Proxmox]
tags: [proxmox,virtualization]
toc: true
---

Add this when you want to get Tun/TAP working to the container config file found in proxmox shell in /etc/pve/lxc/

```conf
lxc.cgroup.devices.allow: c 10:200 rwm
lxc.mount.entry: /dev/net/tun dev/net/tun none bind,create=file
```

### Add New User

```shell
useradd -m garrett
```

```shell
user mod -aG sudo garrett
```