---
title: "Getting started with Docker"
categories:
  - Docker
tags:
  - Docker
  - Docker Compose
  - Docker Swarm
---

## Useful commands

Login to client

```sh
docker login --username staieb
```

```sh
docker stop <Container_ID>
docker rm <Container_ID>
# force remove
docker rm -f < Container_ID>
```

## Docker Swarm

```sh
# create the docker swarm (adds the machine to docker swarm)
docker swarm init
```

```sh
# stack: networks, services and all the containers
docker stack deploy –compose-file=docker-compose.yml mystack

docker service ls

docker service update --image my-app:v2 myapp
docker service scale myapp=3
```
