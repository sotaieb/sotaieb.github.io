---
title: "Getting started RabbitMQ"
categories:
  - messaging
tags:
  - messagin
  - rabbitmq
---

1- Download rabbitMQ Server
2-

```sh
# enable management ui
# http://localhost:15672/
rabbitmq-plugins.bat enable rabbitmq_management

# create a user "admin" with password "admin"
rabbitmqctl add_user full_access admin

# tag the user with "administrator" for full management UI and HTTP API access
rabbitmqctl set_user_tags admin administrator

#delete user
rabbitmqctl delete_user admin
```

## Advanced Message Queuing Protocol (AMQP)

Client for node: amqp.node

## Links

<https://www.rabbitmq.com/>
