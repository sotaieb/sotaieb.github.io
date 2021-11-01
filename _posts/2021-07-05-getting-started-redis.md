---
title: "Getting started Redis"
categories:
  - redis
tags:
  - redis
---

## Installation

Change root password in WSL:

```bash
wsl -u root
passwd username

# Then
wsl
```

Install redis from source

```sh
sudo apt-update
sudo apt install make
sudo apt install build-essential tcl
sudo apt install pkg-config

wget http://download.redis.io/redis-stable.tar.gz
tar xvzf redis-stable.tar.gz
cd redis-stable

# compile
#make MALLOC=libc
make distclean

# install the binaries onto the system
sudo make install

sudo mkdir /etc/redis
sudo cp redis.conf /etc/redis
```

Create a user/group

```sh
sudo adduser --system --group --no-create-home redis
```

Create a redis volume directory

```sh
sudo mkdir /var/redis
sudo chown redis:redis /var/redis
sudo chmod 770 /var/redis
```

Create a Redis systemd

```sh
sudo nano /etc/systemd/system/redis.service
# [Unit]
# Description=Redis
# After=network.target

# [Service]
# User=redis
# Group=redis
# ExecStart=/usr/local/bin/redis-server /etc/redis/redis.conf
# ExecStop=/usr/local/bin/redis-cli shutdown
# Restart=always

# [Install]
# WantedBy=multi-user.target
```

```sh
sudo systemctl enable redis.service
sudo systemctl start redis
systemctl status redis

```

Install redis from repository

sudo apt install redis-server

To remove completely redis-server

```sh
sudo apt-get purge --auto-remove redis-server
sudo service redis_version stop
sudo rm /usr/local/bin/redis-*
sudo rm -r /etc/redis/
sudo rm /var/log/redis_*
sudo rm -r /var/lib/redis/
sudo rm /etc/init.d/redis_*
sudo rm /var/run/redis_*​
```

## Configuration

```sh
# /etc/redis/redis.conf
dir /var/redis
supervised systemd
maxmemory 1gb
maxmemory-policy allkeys-lru
```

```sh
sudo service redis-server start
redis-cli
ping
config get maxmemory
```

## Environment

Access from wsl to windows:

```sh
cat /etc/resolv.conf
nameserver 192.168.1.100
```

Access from windows to redis wsl

```sh
 npm install -g redis-cli
 rdcli -h 172.20.160.1
```
