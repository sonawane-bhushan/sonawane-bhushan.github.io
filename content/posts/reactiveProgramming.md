---
title: "Reactive Programming"
date: 2023-05-27T11:30:03+00:00
weight: 1
aliases: ["/reactive-programming"]
tags: ["reactive", "programming", "beginners"]
author: "Bhushan Sonawane"
showToc: true
TocOpen: false
draft: false
hidemeta: false
comments: true
description: "What, Why and How About Reactive Programming"
canonicalURL: "https://canonical.url/to/page"
disableHLJS: true # to disable highlightjs
disableShare: false
disableHLJS: fals
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

Reactive Programming by definition is a programming paradigm that focuses on building systems that are responsive, resilient, and scalable. It provides a way to handle and react to streams of data as they occur, rather than explicitly programming steps to be executed in a specific order.

Reactive programming is a programming paradigm that deals with data flows and the propagation of change. It means that when a data flow is emitted by one component, the change will be propagated to other components by reactive programming library. The propagation of change will continue until it reaches the final receiver.

Reactive Programming is based on the idea of asynchronous processing. Asynchronous processing means processing of one event does not block other events.

## Why Reactive Programming ? 

It is needed to improve the user experience and make the system more responsive, resilient and scalable. We want to deliver a smooth user experience without blocking the main thread. 

Today reactive programming is widely used in real time applications, mobile applications, IoT applications, microservices etc. 

Basically, Reactive programming is programming with asynchronous data streams. Reactive programming also has its own [reactive manifesto](https://www.reactivemanifesto.org/)

## The Observer Pattern 

As we saw above reactive programming is asynchronous processing of the events. In contrast to synchronous calls where parameters are passed to procedure and program flow has to wait till the result of the procedure is received, the caller doesn’t wait for the response in asynchronous processing. Instead the caller registers a callback procedure that needs to be performed once the result is received. The caller can continue its work and the callback procedure gets called once the result is received. In other words, the caller passes to asynchronous procedure what to do once the result is received. 

Moreover, the caller can register multiple callbacks, this is useful when the caller wants to be notified when something has failed. In this scenario the caller can send two callback procedures one in case of success and one in case of failure. 

The callback thing in object oriented programming is generally implemented using the Observer Pattern. In the observer pattern, the return value of the asynchronous procedure call is called the Observable, and the callback procedure is called the Observer:

![Observer Pattern](/images/ObserverPattern.png)

## Reactive Streams

In reactive applications, handling simply one event doesn’t do the job. Instead, the application has to handle a stream of events. You are able to create data streams of anything, not just from click and hover events. On top of that, you are given an amazing toolbox of functions to combine, create and filter any of those streams. That's where the "functional" magic kicks in. A stream can be used as an input to another one. Even multiple streams can be used as inputs to another stream. You can merge two streams. You can filter a stream to get another one that has only those events you are interested in. You can map data values from one stream to another new one.

A stream is a sequence of ongoing events ordered in time. It can emit three different things: a value (of some type), an error, or a completed signal. We capture these emitted events asynchronously by defining a function which will be called when value is emitted, another function when an error is emitted and another function when complete signal is emitted. Listening to the stream is called subscribing. The functions we are defining are observers. The steam is being observed. 

There are several frameworks and libraries available that support reactive programming, such as RxJava, Reactor, Akka, and Project Reactor. These frameworks provide abstractions and utilities to work with reactive streams and enable developers to build responsive and scalable applications.

## Advantages of Reactive Programming

The driver for using reactive programming is efficient resource utilization, or in other words, spending less money on servers and data centers. The promise of Reactive is that you can do more with less, specifically you can process higher loads with fewer threads. This is where the intersection of Reactive and non-blocking, asynchronous I/O comes to the foreground. For the right problem, the effects are dramatic. For the wrong problem, the effects might go into reverse.

1. Responsiveness: Reactive systems are designed to be highly responsive, providing quick and timely feedback to users. They can handle and react to incoming events and data streams in real-time, which is crucial for applications that require quick and interactive responses.

2. Scalability: Reactive programming enables the development of scalable systems that can efficiently handle a large volume of concurrent requests. By leveraging asynchronous and non-blocking techniques, reactive systems can efficiently utilize system resources and easily scale horizontally to accommodate increasing workloads.

3. Resilience: Reactive programming promotes building resilient systems that can handle failures and errors gracefully. Through techniques like error handling, fault tolerance mechanisms, and circuit breakers, reactive systems can isolate and recover from failures, ensuring overall system stability.

4. Flexibility: Reactive programming provides a flexible programming model that allows developers to compose and transform streams of data easily. This composability makes it simpler to build complex systems by combining and manipulating streams using a set of declarative operators and functions.

5. Efficiency: Reactive programming emphasizes efficient resource utilization, especially in scenarios where resources like CPU, memory, or network bandwidth are limited. By using non-blocking I/O operations and applying back pressure mechanisms, reactive systems can avoid resource wastage and achieve higher efficiency.

6. Event-driven and real-time capabilities: Reactive programming is well-suited for event-driven and real-time applications. It allows developers to handle continuous streams of events and data as they occur, making it easier to build systems that react to changes in real-time and provide live updates.

7. Better code organization and maintainability: Reactive programming encourages a more declarative and functional style of programming, which can lead to cleaner and more maintainable code. By focusing on composing and transforming data streams, developers can build modular and reusable components, leading to code that is easier to understand, test, and maintain.

***

This was all about reactive programming in general, why they were added, what they do and how to use them. Hope it was helpful to you folks. Thank you so much for reading. 

Happy Coding !!


## References
 
1. [The introduction to Reactive Programming you've been missing](http://andre.staltz.com/)
2. [Reactive Programming - Baeldung](https://www.baeldung.com/cs/reactive-programming)
3. [Reactive Programming - Spring.io](https://spring.io/blog/2016/06/07/notes-on-reactive-programming-part-i-the-reactive-landscape)
4. [Reactive Manifesto](https://www.reactivemanifesto.org/)
