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

Update ServerName, ServerAlias, and DocumentRoot where it mentions domain.com, you change it with your domain/subdomain. You can also change the port number from :81 to whatever port you are porting your site off of. You will have to make sure that you edit your /etc/apache2/ports.conf. This file contains the ports that your IP address listens to. 
 
To edit this file, run the following command: 

```shell
sudo nano /etc/apache2/ports.conf
```

In this file where you see Listen 80, you will add whatever port you are wanting to Listen to. 
 
For an example, the grayed out "Listen 81" is what I added in because that is the port I want my Ipaddress to listen to.

```conf
# If you just change the port or add more ports here, you will likely also
# have to change the VirtualHost statement in
# /etc/apache2/sites-enabled/000-default.conf

Listen 80
Listen 81

<IfModule ssl_module>
Listen 443
</IfModule>

<IfModule mod_gnutls.c>
Listen 443
</IfModule>

# vim: syntax=apache ts=4 sw=4 sts=4 sr noet
```

## Step 6 
Now its time to enable your config files. You will first have to disable the 000-default.conf file. You will do this by running the following command:

```shell
sudo a2dissite 000-default.conf
```

You will now enable your conf files by running the following command:

```shell
sudo a2ensite domain.com.conf
```

You will now have to restart the apache2 service so it will register our changes.

```shell
sudo systemctl restart apache2
```

## Step 7
### Confirmation Step

This is an important step, we want to check whether or not our sites are up and running. You will do this but going to your ip address. For example if your ip address is 192.168.1.10 you will want to go to the port you used for the site, so it will be 192.168.1.10:80, where the :80 is your port you are trying to go to. Once you can see that they are working, then we will move on to the steps that lead us into the Cloudflare steps.

# Cloudflare Steps

## Step 1

In this step, we are going to create a tunnel, but if you already have a tunnel created then you can jump to step () and you can skip the tunnel set up steps. To create a tunnel, you will run this command to get your connection authorized. 

```shell
cloudflared tunnel login
```

This will either redirect you or give you a link that you can copy which is where you will authorize your site for the tunnel login. After this step you will create the tunnel with the following command:

```shell
cloudflared tunnel create <your tunnel name>
```

In this command you will replace <your tunnel name> with a desired name for your tunnel. It can be anything usually a one word name. This will create the tunnel within your cloudflare. You can check your tunnel list at your https://dash.teams.cloudflare.com/ URL which you can access through your cloudflare dashboard or you can run a command to list the current tunnels you have running. Keep track of the tunnel ID(UUID), you will need this later on. It will also create a file that has your tunnel ID(UUID).json. This is what controls the tunnel credentials file for you and you will need it later on when we configure your config.yaml file. 

```shell
cloudflared tunnel list
```

This command will help you see if the tunnel you created was actually created. If not, just try again with the tunnel create command above. 
 
The next part of this step would be to create the config.yaml file, but if you already have one then you can just edit the file. I am going to show two different ways to have your config.yaml file. One will be to just have one site running on your tunnel and the second one will allow for you to have multiple domains/sub domains. If you don't have the command, run the following command:

```shell
sudo nano config.yaml
```

Once you get this file created, you will put this information in for a single domain/sub domain and just make sure you update it with the information you need, which I will point out for you.

```yml
url: http://localhost:81
tunnel: <tunnel UUID>
credentials-file: /<path_to_config.yaml>/<tunnel UUID>.json
```

I am going to go through the parts of this config.yaml file. In the url you will keep "http://localhost:81", where the :81 is the port for this specific domain/sub domain that you used back in step 5.. In the tunnel line, you will put the tunnel UUID from when you created the tunnel. In the credentials line, you are going to place the full path of the credentials file. To find the exact path of your config.yaml file, you can run the following command:

```shell
readlink -f config.yaml
```

That was to set up the config.yaml file for a single domain/sub domain, but for multiple domains/sub domains to run through a single tunnel, we will use ingress. For this you will post in the following information:

```yml
tunnel: <tunnel UUID>
credentials-file: /<path_to_config.yaml>/<tunnel UUID>.json

ingress:
  - hostname: domain.us
    service: http://localhost:80
  - hostname: domain.subdomain.us
    service: http://localhost:81

# you'll always need this at the end as the "catch all" for if an endpoint is not found
service: http_status:404
```

You can see the difference between the single domain/sub domain config file and the multiple domain/sub domain config file. The main difference is that you don’t have the URL line in the multiple domain/sub domain config file because you are using the ingress method. Under the ingress you will put hostname: which is going to be how you access it instead of the ip address(of course this hostname is going to have to be a domain that you have purchased, if you it’s a sub domain, it will be a sub of the domain that you purchased). And then you will see the service line, this will be where you put "http://localhost:80", where the :80 is the port number that you used for that subdomain back in step 5. 
 
The next part of this step is routing your traffic. This will be done by using the following command: 

```shell
cloudflared tunnel route dns <TUNNEL NAME> <hostname>
```

In using this command, you will need to replace <TUNNEL NAME> with the tunnel name you assigned to your tunnel when you created it above. And then the host name will be either the URL you put on the url line in the single domain/sub domain config file or the hostname in the ingress section in the multiple domain/sub domain config file. If you are creating a tunnel route for the single domain/sub domain you will just have to do this once, but if you are doing it for multiple domains/sub domains, then you will have to do a single command of the "cloudflared tunnel route dns <NAME> <hostname>" for each domain/sub domain you have on the multiple config file.
 
We are going to now run your tunnel to make sure your domain/sub domains are up and running. You should be able to go to the domain or sub domain to get to the site to see if its working correctly. 
 
If you currently have a tunnel running, you will need to follow some steps but if you don't have a tunnel currently running, then skip to the next section. If you have a tunnel running, you will need to disable the Daemon that you have up and running. Run the next commands in a line:

```shell
sudo systemctl stop cloudflared
```

```shell
sudo systemctl disable cloudflared
```

```shell
sudo rm /etc/cloudflared/config.yml
```

This is going to remove the activated Daemon that you have running. Once you remove that, then you will be able to run you tunnel correctly. If you don’t have a tunnel currently running, then this is where you are going to need to be. To get your tunnel up and running manually, you will need to run this command:

```shell
cloudflared tunnel --config /<path_to_config.yaml>/config.yaml run
```

Where you will replace <path_to_config.yaml> with the path to your config.yaml file. And then this will run your tunnel and you should be able to get to your site via your domain or sub domain. If you are able to get to your domain/sub domain via domain.com  or subdomain.domain.com, then you know its working and its time to put up your tunnel via a Daemon. To do this, you need to run the following simple code if you are in the directory that your config file is in:

```shell
sudo cloudflared --config config.yaml service install
```

Run the following code if you are not in the folder that includes your config.yaml file:

```shell
sudo cloudflared --config /<path_to_config.yaml>/config.yaml service install
```

After running that script, then you will need to run the following commands in consistent to finish up the Daemon set-up:

```shell
sudo systemctl start cloudflared
```

```shell
sudo systemctl enable cloudflared
```

You should now be able to access your site anywhere on any network. If you want to go back and add another subdomain you can just go back and follow these steps.

#  Extra Steps
## Set up CloudFlare HTTPS

- Navigate to dash.cloudflare.com and select your website
- Go to the SSL > Overview, and change the SSL/TLS encryption mode to either Full or Full (strict)
- This image has an empty alt attribute; its file name is Screenshot-2022-03-16-151529-1024x662.png
- I’d also recommend turning on SSL/TLS Recommender to see what else you probably should do for encryption in the future.
- Navigate to SSL > Edge Certificates
- Turn on Always Use HTTPS