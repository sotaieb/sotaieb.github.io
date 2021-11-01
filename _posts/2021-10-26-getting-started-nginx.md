---
title: "Getting started Nginx"
categories:
  - nginx
tags:
  - nginx
---

## Configuration

```sh
vi /etc/nginx/sites-available/default
```

Nginx configuration file forwards incoming public traffic from port 80 to port 5000.

```javascript
server {
  listen 80;
  location / {
    proxy_pass http://localhost:5000;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection keep-alive;
    proxy_set_header Host $host;
    proxy_cache_bypass $http_upgrade;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
  }
}
```

Check Syntax of the Default file

```sh
 sudo nginx -t
```

emctl enable kestrel-Webnix.service

Run the app

```sh
chmod u+x myapp.dll
dotnet myapp.dll
```

Running our application as a service systemd

````sh
vi /etc/systemd/system/myapp.service

 sudo systemctl enable myapp.service
 sudo systemctl start myapp.service
 sudo systemctl status myapp.service

```

```sh
[Unit]
Description=mayapp

[Service]
WorkingDirectory=/var/www/publish
ExecStart=/usr/bin/dotnet /var/www/publish/myapp.dll
Restart=always
# Restart service after 10 seconds if the dotnet service crashes:
RestartSec=10
KillSignal=SIGINT
SyslogIdentifier=myapplogs
Environment=ASPNETCORE_ENVIRONMENT=Production

[Install]
WantedBy=multi-user.target
````

Windows + Kestrel (18808)
Linux + Kestrel (10667)
Windows + IIS In Process (10089)
Linux + Nginx (3509)
Linux + Caddy (3485)
Windows + IIS Out of Process (2820)

Reverse proxy servers provide the following features:

Load Balancing - Reverse Proxy Servers can control incoming requests and reroute them to a designated group of servers. Distributing load on multiple servers increases speed and capacity utilization.

Web Acceleration - Reverse proxy servers can provide features like data compression, caching, SSL encryption, etc., and leaving upstream servers free to do what they are supposed to do.

Security and Anonymity - By sitting in front of the internal network, reverse proxy servers can add another layer of protection against security attacks.
