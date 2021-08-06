---
published: true
title: How to setup xampp with multiple ports on Windows
tags:
  - PHP
  - XAMPP
  - Windows
---
xampp default port is 80 and 443 if you have many project and want to setup project with difference port in xampp called “Virtual Hosts” this is how to setup virtual host in xampp for difference port.

<!--more-->

Update **C:\xampp\apache\conf\httpd.conf** add new listening port

```shell
Listen 80
Listen 81 // new listening port
```

Update **C:\xampp\apache\conf\extra\httpd-vhosts.conf** add new virtual host config

```shell
<VirtualHost *:81> 
    DocumentRoot "C:\xampp\htdocs\mywebsite"
    ServerName 127.0.0.1:81
    <Directory "C:\xampp\htdocs\mywebsite">
        Options -Indexes +FollowSymLinks +MultiViews
        AllowOverride All
        Require all granted
    </Directory>
    
	LogFormat "%h %l %u %t \"%r\" %>s %b \"%{Referer}i\" \"%{User-agent}i\"" combined
    SetEnvIf Request_URI "(\/assets\/)" dontlog
    CustomLog C:\xampp\Apache24\logs\myweb-access.log combined env=!dontlog
    ErrorLog C:\xampp\Apache24\logs\myweb-error.log
    
</VirtualHost>
```

Update **C:\Windows\System32\drivers\etc\hosts** add new host and port in this file.

```shell
# 127.0.0.1       localhost
# ::1             localhost
127.0.0.1      127.0.0.1:81 // host with new port.
```

Restart apache again and test a new address in browser.
