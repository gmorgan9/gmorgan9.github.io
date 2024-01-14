---
layout: post
title:  "CloudFlare Custom Email"
date: 2024-01-14 10:00:00 -500
categories: [CloudFlare]
tags: [cloudflare,custom-email]
toc: true
---

## Basic Steps

1. On Cloudflare go to email
2. Enter in the desired custom email
3. Have it send to a Gmail account(hosting it on that Gmail account)

## Next Steps

1. Go to you Google account settings → Security → App Passwords → Create one that goes to your mail and give it a unique name.
2. Copy the gibberish password it gives you (you wont be able to see it again).
3. Go to Gmail → settings → see all settings → Accounts and Import → Add another email address
4. Enter a name as to what you want your senders to see and enter you new custom email address. Make sure to uncheck “Treat as Alias” and click “Next Step”.
5. On the next screen, you will make sure the SMTP server is “smtp.gmail.com” and port 587. In the Username field, enter your main google email account. In the Password field, enter the one-time app password created earlier.

You should be able to now send and receive emails from that account on your gmail account. You will probably see something pop-up on Cloudflare and say that there are DNS records that need to be added to your DNS. You can just click add records automatically, and it will automatically add them for you.