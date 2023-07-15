---
title: "Java Evolution: From Auto-Generated Getters and Setters to Java Records"
excerpt: "Explore the shift from auto-generating getters and setters to embracing Java Records, in the context of Domain-Driven Design and functional programming."
last_modified_at: 2023-05-01T04:46:00-05:00
categories:
  - java
  - domain-driven-design
  - functional-programming
tags: 
  - java
  - records
  - domain-driven-design
  - functional-programming
  - lombok
  - ide
---
## Introduction

In this blog series, we aim to guide Java developers who are returning to the language and want to catch up with the latest best practices. Today, we'll explore the historical shift from auto-generating getters and setters to using Java Records, and how this evolution has been influenced by the rise of Domain-Driven Design and functional programming.

## The Old Way: Auto-Generating Getters and Setters with IDE

In the early days of Java, creating a class meant writing private fields and then generating public getters and setters for those fields. This was a tedious and verbose process, often leading to bloated classes filled with boilerplate code. Here's an example:

```java
public class Employee {
    private int id;
    private String name;

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}
```

## The Interim Solution: Project Lombok

Project Lombok emerged as a solution to reduce the verbosity of Java classes. It provided annotations like `@Data` to auto-generate getters, setters, `equals()`, `hashCode()`, and `toString()` methods. Here's how our `Employee` class would look with Lombok:

```java
import lombok.Data;

@Data
public class Employee {
    private int id;
    private String name;
}
```

However, Lombok had its limitations. It required a plugin to be installed in your IDE and build system, and there were potential issues with IDE support and debugging.

## The Rise of Domain-Driven Design and Functional Programming

In recent years, there has been a growing interest in Domain-Driven Design (DDD) and functional programming. DDD is an approach to software design that prioritizes the 'domain,' or the sphere of knowledge and activity that the software addresses. It emphasizes a model-driven design where the model is based on the actual domain[^1].

Functional programming, on the other hand, is a programming paradigm that treats computation as the evaluation of mathematical functions and avoids changing state and mutable data[^2].

In DDD, a key concept is the Value Object. A Value Object is an immutable object that contains attributes but has no conceptual identity. They are used to represent objects that are entirely defined by their state, not by a thread of continuity or identity[^3].

Functional programming also emphasizes immutability and the use of pure functions that do not have side effects. These trends have led to a demand for a more concise and functional-friendly syntax in Java.

The rise of these concepts has influenced the design and evolution of Java, leading to the introduction of features like Java Records. These features align with the principles of DDD and functional programming, providing developers with powerful tools to design software that is more robust, maintainable, and aligned with the business domain.

[^1]: [Domain-driven design in functional programming | Thoughtworks](https://www.thoughtworks.com/insights/blog/architecture/domain-driven-design-in-functional-programming)
[^2]: [Functional Domain Driven Design: Simplified - Antman writes software](https://antman-does-software.com/functional-domain-driven-design-simplified)
[^3]: [Domain Modeling Made Functional - The Pragmatic Programmer](https://pragprog.com/titles/swdddf/domain-modeling-made-functional/)

## The Modern Approach: Embracing Java Records

With the rise of Domain-Driven Design and functional programming, Java has evolved to accommodate these trends. A significant step in this evolution is the introduction of Java Records, a feature that was previewed in Java 14 and officially released in Java 16.

Java Records provide a compact syntax for declaring classes, which automatically creates immutable classes with public accessor methods. This feature significantly reduces the verbosity of Java code, making it more readable and maintainable.

Let's revisit our `Employee` class, this time as a Java Record:

```java
public record Employee(int id, String name) {}
```

In this single line of code, we've declared a class `Employee` with two fields, `id` and `name`. Java Records automatically creates a constructor, accessor methods, `equals()`, `hashCode()`, and `toString()` methods. This is a stark contrast to the verbose code we had to write in the past.

Java Records align perfectly with the concept of Value Objects in Domain-Driven Design. They are immutable, meaning their state cannot be changed after they are created, and they are value-based, meaning that two record instances are equal if they contain the same state.

Here's an example of a `Money` Value Object represented as a Java Record:

```java
public record Money(String currency, BigDecimal amount) {}
```

The introduction of Java Records marks a significant step in Java's evolution, providing a modern approach to writing concise, expressive, and functional-friendly code.

## Embracing the Future: The Power of Java Records

The evolution from auto-generating getters and setters with IDE, to Project Lombok, and finally to Java Records, shows the continuous strive for simplicity and expressiveness in Java. Java Records not only reduce boilerplate code but also provide a perfect match for implementing Value Objects in Domain-Driven Design.

In our next blog post, we'll delve into another exciting topic in modern Java development. Stay tuned and keep coding!
