---
layout: post
title:  "New Server User Set up (Linux)"
date: 2024-01-14 10:00:00 -600
categories: [Linux]
tags: [linux,new-user]
toc: true
---

## Create new user

- First add the user, run: sudo adduser `<UserNameHere>`
- Add the user to sudo group by typing the command in terminal for Ubuntu version 12.04 and above: sudo adduser `<UserNameHere>` sudo
- In an older version of Ubuntu (version 12.04 and older), run:
    - sudo adduser `<UserNameHere>` admin
- Verify it: id <`UserNameHere>`

## Other Steps

- Allow login with ssh for root
- Make sure you have a 192.168.1.# ip address with ip a
- Install TailScale