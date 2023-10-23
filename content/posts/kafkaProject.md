---
title: "Kafka Series - 2 Creating Kafka Consumer and Producer in Java Spring Boot Application"
date: 2023-09-30T11:30:03+00:00
weight: 1
aliases: ["/kafka-series-2"]
tags: ["kafka-setup", "kafka", "local", "java", "springboot", "beginners"]
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

## Consuming and publishing kafka messages in spring boot

In the last blog we saw how to bring up a kafka cluster on local and publish-consume messages using kafka-console commands. In this blog we will take a look at how to connect your spring boot application to kafka cluster and publish-consume messages via it.

Let’s start by creating producer first 

1. Go to https://start.spring.io/ 
2. Select Spring Web and Spring for Apache Kafka dependencies and generate the project
3. Next open project in any of the code editor  

Now, we will have to create a kafka config, to do that create a PublisherConfig class 

```java:
package com.bhushan.kafka.config;

import com.bhushan.kafka.model.Student;
import org.apache.kafka.clients.producer.ProducerConfig;
import org.apache.kafka.common.serialization.StringSerializer;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.kafka.core.DefaultKafkaProducerFactory;
import org.springframework.kafka.core.KafkaTemplate;
import org.springframework.kafka.core.ProducerFactory;
import org.springframework.kafka.support.serializer.JsonSerializer;

import java.util.HashMap;
import java.util.Map;

@Configuration
public class PublisherConfig {
    @Bean
    public ProducerFactory<String, Student> producerFactory() {
        Map<String, Object> config = new HashMap<>();

        config.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, "127.0.0.1:9092");

        config.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, StringSerializer.class);

        config.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class);
        
        return new DefaultKafkaProducerFactory<>(config);
    }

    @Bean
    public KafkaTemplate<String, Student>
    kafkaTemplate() {
        return new KafkaTemplate<>(producerFactory());
    }
}
```


Instead of creating a configuration class we can also configure kafka using application.properties file 

```
# Producer properties
spring.kafka.producer.bootstrap-servers=127.0.0.1:9092
spring.kafka.producer.key-serializer=org.apache.kafka.common.serialization.StringSerializer
spring.kafka.producer.value-serializer=org.apache.kafka.common.serialization.StringSerializer
spring.kafka.producer.group-id=group_id
topic.name.producer=topico.comando.teste

# Common Kafka Properties
auto.create.topics.enable=true
```

Second step is to create a data class whose object we will be sending as message using our producer 

Create class Student.java

```java:
package com.bhushan.kafka.model;

public class Student {

    int id;
    String firstName;
    String lastName;

    public Student(int id, String firstName, String lastName) {
        this.id = id;
        this.firstName = firstName;
        this.lastName = lastName;
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }


    public String getFirstName() {
        return firstName;
    }

    public void setFirstName(String firstName) {
        this.firstName = firstName;
    }

    public String getLastName() {
        return lastName;
    }

    public void setLastName(String lastName) {
        this.lastName = lastName;
    }
}
```

Third we will create a controller endpoint upon hitting which we will be sending a message to kafka topic 

Create PublishController.java

```java:
package com.bhushan.kafka.web;

import com.bhushan.kafka.model.Student;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.kafka.core.KafkaTemplate;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RequestMapping("kafka")
public class PublishController {
    @Autowired
    private KafkaTemplate<String, Student> kafkaTemplate;


    private static final String TOPIC = "first_kafka_topic";


    @GetMapping("/publish/{id}/{firstName}/{lastName}")
    public String post(
        @PathVariable("id") final int id,
        @PathVariable("firstName") final
        String firstName,
        @PathVariable("lastName") final
        String lastName) {


        kafkaTemplate.send(TOPIC, new Student(id, firstName, lastName));


        return "Published successfully";
    }
}
```

This is it. This is our kafka publisher, once hitting /publish endpoint a message will be published to ‘first_kafka_topic’, we can test this by bringing up this application, and using kafka-console-consumer to consume the data


Creating Kafka consumer in spring boot 

Follow the same steps as producer and create a project on spring initializer. 

First we will create consumer configuration 

KafkaConsumerConfig.java

```java:
package com.bhushan.kafka.config;


@EnableKafka
@Configuration
public class KafkaConsumerConfig {


    @Bean
    public ConsumerFactory<String, String> consumerFactory() {
        Map<String, Object> props = new HashMap<>();
        props.put(
          ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG, 
          bootstrapAddress);
        props.put(
          ConsumerConfig.GROUP_ID_CONFIG, 
          groupId);
        props.put(
          ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG, 
          StringDeserializer.class);
        props.put(
          ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, 
          StringDeserializer.class);
        return new DefaultKafkaConsumerFactory<>(props);
    }


    @Bean
    public ConcurrentKafkaListenerContainerFactory<String, String> 
      kafkaListenerContainerFactory() {
   
        ConcurrentKafkaListenerContainerFactory<String, String> factory =
          new ConcurrentKafkaListenerContainerFactory<>();
        factory.setConsumerFactory(consumerFactory());
        return factory;
    }
}
```

Then create same Student.java data class as that of producer 

Once that is done we will  be creating a message consumer to consumer messages on “first_kafka_topic” 

Create MessageConsumer.java

```java:
package com.bhushan.kafka.consumer;


import com.bhushan.kafka.model.Student;
import org.springframework.kafka.annotation.KafkaListener;
import org.springframework.messaging.handler.annotation.Payload;
import org.springframework.stereotype.Service;


@Service
public class MessageConsumer {

    @KafkaListener(topics = {"first_kafka_topic"}, groupId = "foo9", containerFactory = "kafkaListenerContainerFactory")
    public void listenGroupFoo(@Payload Student message) {
        System.out.println("Received Message in group foo: " + message);
    }

}

```

Bring up both consumer and producer at same time, and try hitting publish endpoint of producer, you should be able to message getting printed in logs of consumer 

Congratulations, You did it 🥳!!

Next we will try to find a problem with our implementation and how to fix it.  


***

Happy Coding !!


## References
 
1. [Kafka Docs](https://kafka.apache.org/documentation/)
