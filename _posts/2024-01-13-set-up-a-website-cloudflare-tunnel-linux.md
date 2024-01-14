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

```shell
sudo mkdir -p /var/www/domain.com/public_html
```

## Step 2
You are going to need to change the permission of the folder. Do this by running the following command:

```shell
sudo chmod -R 755 /var/www
```

## Step 3
It's time to create the Index Page for your website. You are going to need to create an index.html file for each domain.  This step is going to show you how to create one index.html for one domain, but you will do the same thing for each domain if you have multiple. 
 
Create index.html within your directory you created in step 1(you can use whatever text editor you want, I use nano).

```shell
sudo nano /var/www/domain.com/public_html/index.html
```

You can add any text that you want within the document but for a test document while we make sure that we can get the website up and running, you can put just a test line of text like this.

```shell
testing for domain.com
```

You will need to create an index.html file for each of the sites you are creating.

## Step 4
Now its time to copy the config files for each site. You will do this by running the following command:

```shell
sudo cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/domain.com.conf
```

You will need to do this for each of the sites that you are creating, just make sure you change the "domain.com" to your domain/subdomain name.

## Step 5
Now it is time to edit the config file that you just copied for your site. You will do this by running this code(again using the text editor that you desire). 

```shell
sudo nano /etc/apache2/sites-available/domain.com.conf
```

Now inside your file, you will add/edit the lines.

```conf
<VirtualHost *:81>
ServerAdmin webmaster@localhost
ServerName http://domain.com 
ServerAlias http://www.domain.com 
DocumentRoot /var/www/domain.com/public_html
ErrorLog ${APACHE_LOG_DIR}/error.log
CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```