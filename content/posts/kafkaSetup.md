---
title: "Kafka Series - 1 Setting up kafka on local machine"
date: 2023-09-27T11:30:03+00:00
weight: 1
aliases: ["/kafka-series-1"]
tags: ["kafka-setup", "kafka", "local", "docker", "beginners"]
author: "Bhushan Sonawane"
showToc: true
TocOpen: false
draft: false
hidemeta: false
comments: true
description: "Kafka Series 1 - Setting up kafka on local, and publising-consuming events using kafka-console commands"
canonicalURL: "https://canonical.url/to/page"
disableHLJS: true # to disable highlightjs
disableShare: false
disableHLJS: false
hideSummary: false
searchHidden: true
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowWordCount: true
ShowRssButtonInSectionTermList: true
UseHugoToc: true
cover:
    image: "<image path/url>" # image path/url
    alt: "<alt text>" # alt text
    caption: "<text>" # display caption under cover
    relative: false # when using page bundles set this to true
    hidden: true # only hide on current single page
---

According to Kafka website, “Apache Kafka is an open-source distributed event streaming platform used by thousands of companies for high-performance data pipelines, streaming analytics, data integration, and mission-critical applications”

Before diving into how kafka works and how to configure it for your specific use case, we will learn as part of this blog on how to bring up a kafka cluster on a local dev machine along with zookeeper and publishing-consuming events using kafka-console commands. 


## Common terminologies of Kafka

1. Event - event is a record of something happening on your system. Event is also called a message. We read/write on kafka in the form of events. 

2. Topics - events are stored in topics. Topics are similar to folders in the filesystem and events are files inside the filesystem. Topics are multi-publisher and multi-subscriber i.e. many systems can write to the topic and read from the topic simultaneously. 

3. Producers - Producer is system which is writing (publishing) message to kafka 

4. Consumers - Consumers are systems which read (and process) the events published to kafka topics

5. Partitions - Topics are partitioned, meaning a topic is spread over a number of "buckets" located on different Kafka brokers. This distributed placement of your data is very important for scalability because it allows client applications to both read and write the data from/to many brokers at the same time.

***

Now let’s get ahead and bring up kafka on local machine

For this you will need docker running on your machine. Refer to [these](https://www.docker.com/get-started/) steps to get docker installed on your machine

We will be using docker-compose to bring up kafka and zookeeper. Zookeeper is often used in conjunction with Kafka to provide critical coordination, management, and configuration capabilities for Kafka clusters.

## Create docker-compose-kafka.yaml 

```
version: '3'

services:
  zookeeper:
    image: wurstmeister/zookeeper
    container_name: zookeeper
    ports:
      - "2181:2181"
  kafka:
    image: wurstmeister/kafka
    container_name: kafka
    ports:
      - "9092:9092"
    environment:
      KAFKA_ADVERTISED_HOST_NAME: localhost
      KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181
```

We are going to use wurstmeister/zookeeper and wurstmeister/kafka images from docker hub. Once this file is created run below command to start kafka and zookeeper. 

```
docker-compose -f docker-compose-kafka.yml up -d
```

For the first time it will download the images for kafka and zookeeper from the artifactory so it will take more time but once downloaded subsequently it will make use of the same downloaded image thus reducing the amount of time required.

![List Images](/images/listing-all-containers.png)

Once this is done we can use below command to exec into the kafka container

```
docker exec -it kafka /bin/sh
```

To run kafka-console commands, change directories to bin

```
cd opt/kafka_<version>/bin
```

Now, lets create a new kafka topic 

```
kafka-topics.sh --create --zookeeper zookeeper:2181 --replication-factor 1 --partitions 1 --topic first_kafka_topic
```

![Creating Topic](/images/creating-topic.png)

To list all the topics 

```
kafka-topics.sh --list --zookeeper zookeeper:2181
```

![Listing Topics](/images/listing-topics.png)

We will read messages on our topic using kafka-console-consumer

```
kafka-console-consumer.sh --bootstrap-server 127.0.0.1:9092 --topic first_kafka_topic
```

Now open another terminal tab and publish a message using below command 

```
kafka-console-producer.sh --topic first_kafka_topic --bootstrap-server 127.0.0.1:9092 <message>
```

![Producing Message](/images/producing-message.png)

You should be able to see the message getting consumed in our consumer tab 

![Consuming Message](/images/consuming-message.png)

Congrats, you have successfully published and consumed your first kafka message 👏

Next, we will see how to do the same in spring-boot application and create your first event-driven application.

***

Happy Coding !!


## References
 
1. [Kafka Docs](https://kafka.apache.org/documentation/)
