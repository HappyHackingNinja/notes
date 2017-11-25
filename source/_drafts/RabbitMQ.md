---
title: RabbitMQ
tags:
---

# RabbitMQ

基於 Advanced Message Queuing Protocol (AMQP) 0.9.1


## AMQP 0-9-1 Model

![](https://www.rabbitmq.com/img/tutorials/intro/hello-world-example-routing.png)

## Exchanges and Exchange Types

| Name             | Default pre-declared names              |
| ---------------- | --------------------------------------- |
| Direct exchange  | (Empty string) and amq.direct           |
| Fanout exchange  | amq.fanout                              |
| Topic exchange   | amq.topic                               |
| Headers exchange | amq.match (and amq.headers in RabbitMQ) |

Exchanges 除了 type，還有以下重要屬性：
* Name
* Durability (`broker` 重啟時保留)
* Auto-delete (當最後一個 `queue` 解除綁定時)
* Arguments (其他可選參數)

### Direct Exchange

當 `routing key` 完全符合時發送

![](https://www.rabbitmq.com/img/tutorials/intro/exchange-direct.png)


### Fanout Exchange

忽略 `routing key`，以廣播方式發送

![](https://www.rabbitmq.com/img/tutorials/intro/exchange-fanout.png)


### Topic Exchange

當 `routing key` 匹配綁定的 pattern 時發送

### Headers Exchange

忽略 `routing key`，當 `headers` 匹配綁定的 pattern 時發送，`headers` 為 hash (dictionary) 

## Queues

* Name
* Durable (the queue will survive a broker restart)
* Exclusive (used by only one connection and the queue will be deleted when that connection closes)
* Auto-delete (queue that has had at least one consumer is deleted when last consumer unsubscribes)
* Arguments (optional; used by plugins and broker-specific features such as message TTL, queue length limit, etc)


## Ref.
* [AMQP 0-9-1 Model Explained](https://www.rabbitmq.com/tutorials/amqp-concepts.html)
* [RabbitMQ Exchange类型详解](http://www.cnblogs.com/julyluo/p/6265775.html)


