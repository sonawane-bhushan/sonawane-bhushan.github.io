---
title: "Lambdas in Kotlin"
date: 2022-05-13T11:30:03+00:00
weight: 2
aliases: ["/kotlin-lambdas"]
tags: ["lambdas", "kotlin", "Kotlin Beginners", "Beginners"]
author: "Bhushan Sonawane"
showToc: true
TocOpen: false
draft: false
hidemeta: false
comments: true
description: "All about lambdas in Kotlin"
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

Lambdas are nothing but anonymous functions; a function without a name, meaning they are function defined without fun keyword and we are allowed to assign them to variables or pass them as functional parameters.

In Kotlin we define a lambda as follows:

```:kotlin
var lambda1 = { name: String -> println('Hello, my name is $name') }
```

In above example name is the input parameter to our lambda function and the statement after `->` is body of our lambda function and we have assigned the lambda to lambda1 variable.

We can use `run` function to run a particular lambdas, for example:

```kotlin:
run { println("Hello World") }
```

this will run the lambda & print Hello World as output

The parameter list can be skipped in case of single argument & type is generally inferred by Kotlin compiler. The type of last command within the lambda is returned type of the lambda function.

### 1. Type Inference

Kotlin type inference allows it to decide type of the lambda function to be evaluated by compiler.

```kotlin:
var sum = { a: Int, b: Int -> 
  val sum = a + b 
  sum.toString()
}
```

Kotlin will evaluate above example as a function which takes two Int and returns a string (Int, Int) -> String

### 2. Type Declaration

Sometimes kotlin compiler is unable to infer the type of our lambda function in that case we have to explicitly declare type for our lambda.

We use Input -> Output for type declaration of lambda if our lambda returns nothing then type is `Unit`

```kotlin:
var sum : (Int, Int -> Int) = { a, b -> a + b }
```

### 3. Passing lambda as function argument

In Kotlin, we can pass function as argument to another function. Also, we can return a function from other function, these functions are called as **higher order functions**. Mostly lambda functions are passed to higher order functions for convenience.

```kotlin:
var sumLambda = { a: Int, b: Int -> a + b }

fun calculator(a: Int, b: Int, operation: (Int, Int) -> Int) {
  var result = operation(a, b)
  println("The result is: $result")
}

fun main(args: Array<String>) {
    calculator(3, 5, sumLambda) // will return "The result is: 8"
}
```

In above example, calculator is a higher order function which takes operation function as an argument.

Lambdas are frequently used while working with collections, And kotlin has several built-in functions in standard library that take lambdas as a argument to make our work easier.

### 4. Passing trailing lambdas

According to kotlin convention, if lambda is the last parameter of the function then lambda expression that is passed can written outside of function parentheses. This syntax is called as trailing lambda.

```kotlin:
val sum = calculator(1, 2){a, b -> a + b}
```

If there is only a lambda in functions argument than function arguments can be omitted completely

```kotlin:
run { println("Hello World") }
```

### 5. it : implicit name of single parameter

It is very common for lambda expression to have single parameter. The shorthand use to represent this single argument is `it`

```kotlin:
val numbers = arrayOf(2, 3, 6, 7)

// without it 
numbers.forEach { item -> println(item) }

// with it 
numbers.forEach { println(it) }
```

***

I hope this article was able to clear the understanding of lambdas work in kotlin. Thanks for reading.

Happy Coding !!


## References
1. [Kotlin Documentation - lambdas](https://kotlinlang.org/docs/lambdas.html)
2. [Javatpoint - Kotlin Lambda Function](https://www.javatpoint.com/kotlin-lambdas)

