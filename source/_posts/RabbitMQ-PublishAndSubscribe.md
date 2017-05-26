---
title: RabbitMQ-PublishAndSubscribe
date: 2017-05-26 00:00:15
tags:
  - MessageQueue
  - Nodejs
---

# Publish and Subscribe

為了說明發布與訂閱

我們將會建立一個簡單的 log system

這包含了兩隻程式

一隻會發布 log

另一隻則會接收並且 print 在 console上

若我們有多個接收的程式

他們就都會接收到同樣的訊息

如此的話

我們就可以一個程式在接收到 Log 後寫入檔案

另一個接收到 Log 則將訊息顯示在螢幕上

也就是說也就是說發布的訊息將會被所有接收者接收

##  Exchanges

我們之前教學的內容

* 生產者負責發送訊息
* Queue 是任務的暫存區
* 客戶是負責接收訊息

RabbitMQ 的核心是生產者不直接發送任何訊息進入Queue

甚至也不知道 Message 發送後會進入哪一個Queue

生產者只需要將 Message 發送給 Exchange 就好了

Exchange 必須十分清楚接收到了訊息之後要如何處理

加入特定的 Queue?

加到多個 Queue?

或是應該捨棄

規則則由 Exchange type 定義

有幾種 Exchange type 可以使用

* Direct
* Topic
* headers
* fanout

這個範例是以 fanout 為主

先建立一個 fanout 類型的 type 命名為 log

```js
ch.assertExchange('logs', 'fanout', {durable: false});
```

fanout 主要就是廣播給所有的 channel 知道

很適合這次的 Log 範例

```js
Listing exchanges

列出可以使用的 Exchange type 可以使用命令列查詢

  $ sudo rabbitmqctl list_exchanges

列表會顯示 amq.*

發送預設的 Exchange

ch.sendToQueue('hello', new Buffer('Hello World!'));
```