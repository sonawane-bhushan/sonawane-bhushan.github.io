---
title: "Null Safety In Kotlin"
date: 2022-04-01T11:30:03+00:00
weight: 1
aliases: ["/null-safety"]
tags: ["null-safety", "kotlin", "Kotlin Beginners", "Beginners"]
author: "Bhushan Sonawane"
showToc: true
TocOpen: false
draft: false
hidemeta: false
comments: true
description: "How kotlin handles null safety to make getting NullPointerExcpetion almost impossible"
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

In this blog we will take a look at how Kotlin handles null references.

Kotlin is a cross-platform, statically typed, general-purpose programming language. Any language that has concept of null reference will have a possibility of throwing NullPointerException, Kotlin on the other hand is designed in such a way that it is really difficult to get a NullPointerException

In this article we will make use of Java to compare how Kotlin behaves differently to null references.

In Java, we are allowed to assign null to variable of any data type & compiler will allow us without any compilation errors, which will in-turn increase a chance of getting a `NullPointerExcpetion`

```java:
String str = null;
str.toUpperCase(); // this will cause a NullPointerException
```

On the other hand Kotlin compiler won’t allow us to assign null to variable unless we declare variable as nullable type.

```kotlin:
val str: String = null    // not allowed 
```

To define a nullable variable, we must append a question mark (?) to the type declaration of variable.

```kotlin:
val str: String? = null
```

In case of non-nullable variables we can directly call method or access property. However in case of variables with nullable data type we need to handle null check explicitly. Otherwise, Kotlin will throw compilation error, as compiler knows it may contain null references.

***

## Working with Nullable Types

Let’s look at the different ways how we can handle null references safely in Kotlin.

### 1. Explicitly check for null using if-else

To perform explicit null check on nullable types we can make use if-else statements. Depending on complexity of condition we may end up writing nested if-else conditions

```kotlin:
val str: String? = null
return if (str != null)
{
    str.upperCase()
}
else
{
     null
}
```

### 2. Safe Call Operator

Kotlin also provides a short hand operator to perform null check similar to what we did using if-else. It is called safe call operator (?). This operator evaluates the expression only if reference is not null else it returns a null value.

Let’s see how to use safe call operator

```kotlin:
val str1: String? = "Hello World"
val str2: String? = null

println("The length of str1 is ${str1?.length}")
println("The length of str2 is ${str2?.length}")

// OUTPUT -
// The length of str1 is 11
// The length of str2 is null
```

Safe call operator can also be chained to perform null checks on class properties. As compared to Java where we need to use if-else chaining to perform null check, in Kotlin it can be achieved in single line of code using safe call operator.

```kotlin:

val employee1: Employee? = Employee(Contact("9123456780", "test@mail.com")) 
val mobileNumber1: String? = employee1?.contact?.mobileNumber

println("Employee 1 mobile number is : ${mobileNumber1}")
// OUTPUT - Employee 1 mobile number is : 9123456780

val employee2: Employee? = Employee(Contact(null)) 
val mobileNumber2: String? = employee2?.contact?.mobileNumber

println("Employee 2 mobile number is : ${mobileNumber2}")
// OUTPUT - Employee 2 mobile number is : null
```

If in safe call chain null is found then whole expression returns null value as we can see in above example when contact is null, the expression returns value as null.

### 3. Elvis Operator

We can use Elvis Operator (?:) to return a default value in case the expression returns a null value. If left side of elvis operator is non-null value the that is returned or else default value (right side of elvis operator) is returned.

```kotlin:
val employee1: Employee = Employee(Address(country="US"))
val country1: String = employee1?.address?.country ?: "INDIA" 

println("Country of Employee1 is : ${country1}")
// OUTPUT - Country of Employee1 is : US

val employee2: Employee = Employee(Address(null))
val country2: String = employee2?.address?.country ?: "INDIA" 

println("Country of Employee2 is : ${country2}")
// OUTPUT - Country of Employee1 is : INDIA
```

As we can see from the example as in first case the expression in returning non null value we will get result of expression as output. In second case as expression evaluates to null hence, we are getting default value from right side of elvis operator. We can also use elvis operator to throw exception if right side of expression returns null.

### 4. Non null Assertion

In a case when we are certain that the value returned from a nullable variable is going to be non-null (which we can never be sure of 😅) there we can make use of non null assertion (!!). It tells the compiler we are absolutely sure variable will be non-null always. This operator converts any reference to its non-nullable type and throws an exception if the reference has a null value. This needs to be used with extra precaution as it may throw `NullPointerException`

```kotlin:
val str1: String? = "Hello World"
println("The length of str1 is ${str1!!.length}")
// OUTPUT - The lenght of str1 is 11

val str2: String? = null
println("The length of str2 is ${str2!!.length}")
// OUTPUT - will throw NullPointerException
```

There might be certain scenarios in which we may want to use this. For eg. We may want program to throw a null pointer exception if certain variable becomes null as our program won’t run properly once that evaluates to null or we need to handle exception in case the value becomes null.

### 5. let() method

When we wan’t to call a function which expects a non-nullable but we only have nullable value in that scenario we can use let() operator. We might think we can directly convert nullable to non-nullable using non null assertion (!!) but as the function we are calling is expecting non-null value it is not responsibility of that function to check for nullability of input parameters hence, we may end up getting an NullPointerException

We can make use of let() function to safely mitigate this problem. It is not designed specifically for this scenario but it is a perfect solution to our problem.

let() function will only perform the function in right side when the left side value evaluates to non-null value, else it will skip it

```kotlin:
var str1: String? = "Hello World"
str1?.let { printText(it) } // will call printText

var str2: String? = null
str2?.let { printText(it) } // printText won't be called as str2 is null
```

In this example as str1 is non-null value printText will be called for str1 but, str2 is null hence for str2 printText will be skipped by let function.

### 6. run() method

Kotlin also has a run() method to perform operations on nullable references based on condition. run() method operates using this & returns result of lambda expression.

```kotlin:
val animals: List<String?> = listOf("Duck", "Horse", null)
val animalsName: List<String?> = emptyList()
for (animal in animals) {
  animal?.run
  { 
    animalName = animalName.plus(this) 
  }?.also{print(it)}
}

// OUTPUT - Duck Horse
```

### 7. also() method

If we want to apply some additional operations on nullable references like printing or logging. also method can also be used by chaining with let() or run() method.

```kotlin:
val animals: List<String?> = listOf("Duck", "Horse", null)
val animalsName: List<String?> = emptyList()
for (animal in animals) {
  animal?.run
  { 
    animalName = animalName.plus(this) 
  }?.also{print(it)}
}

// OUTPUT - Duck Horse
```

***

That’s all about null reference safety in Kotlin !! I hope this article was able to clear the understanding of how null safety works in Kotlin.

Happy Coding !!

## References
1. [Kotlin Documentation - Null Safety](https://kotlinlang.org/docs/null-safety.html)
2. [Geeks for Geeks - Kotlin Null Safety](https://www.geeksforgeeks.org/kotlin-null-safety/)

