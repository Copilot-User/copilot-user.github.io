# Java Evolution: Moving from Field Injection to Constructor Injection in 2023

## Introduction

In this blog series, we aim to guide Java developers who are returning to the language and want to catch up with the latest best practices. Today, we'll explore the historical shift from field injection to constructor injection in Java.

## Field Injection and @Autowired: A Look Back

Field injection and the `@Autowired` annotation were widely used in the past. Here's a typical example:

```java
@Autowired
private CustomerService customerService;
```

However, this approach has several drawbacks:

- It leads to tight coupling.
- It makes testing more difficult.
- It lacks null safety.

Here's a code sample that demonstrates these problems:

```java
@Autowired
private CustomerService customerService;

public void createCustomer() {
    if (customerService == null) {
        throw new IllegalStateException("CustomerService is not initialized!");
    }
    customerService.addCustomer(); // This can lead to a NullPointerException if customerService is not properly initialized.
}
```

In this example, we can see that if `customerService` is not properly initialized, it can lead to a `NullPointerException` when we try to use it. This is a significant problem because it can lead to runtime errors that are hard to debug.

Moreover, field injection makes testing more difficult. When writing unit tests, we need to manually inject the dependencies, which can be cumbersome and error-prone. Here's an example of how you might write a test for the above code:

```java
@Test
public void testCreateCustomer() {
    CustomerController customerController = new CustomerController();
    ReflectionTestUtils.setField(customerController, "customerService", new CustomerService());
    // Now we can call createCustomer without getting a NullPointerException.
    customerController.createCustomer();
}
```

In this test, we're using Spring's `ReflectionTestUtils` to manually inject the `CustomerService` dependency. This is not ideal because it requires us to use reflection, which can be error-prone and make our tests harder to understand.

Field injection also leads to tight coupling. The `CustomerController` class is tightly coupled to the `CustomerService` class, which makes it harder to change or replace `CustomerService` in the future.

For these reasons, field injection is generally not recommended. Instead, we should use constructor injection, which we'll discuss in the next section.

For more information on the drawbacks of field injection, you can refer to these resources:

- [Why Is Field Injection Not Recommended? | Baeldung](https://www.baeldung.com/java-spring-field-injection-cons)
- [What exactly is Field Injection and how to avoid it? - Stack Overflow](https://stackoverflow.com/questions/39890849/what-exactly-is-field-injection-and-how-to-avoid-it)
- [Spring Field Injection Example and It's Drawbacks - Java Guides](https://www.javaguides.net/2019/11/spring-field-injection-example-and-its-drawbacks.html)

## Constructor Injection: The New Paradigm

Constructor injection has emerged as a more robust and flexible approach. It promotes loose coupling, makes testing easier, and ensures null safety by guaranteeing that an instance of a class cannot be created until all of its dependencies are provided. Here's an example:

```java
private final CustomerService customerService;
private final OrderService orderService;

@Inject
public CustomerController(CustomerService customerService, OrderService orderService) {
    this.customerService = customerService;
    this.orderService = orderService;
}
```

In this example, `CustomerService` and `OrderService` are dependencies of `CustomerController`. By making these fields `final`, we ensure that they must be initialized when an instance of `CustomerController` is created. This is done through the constructor, which takes these dependencies as parameters. The `@Inject` annotation tells Spring to use this constructor to inject the dependencies.

Here's a code sample that demonstrates the benefits of constructor injection:

```java
private final CustomerService customerService;
private final OrderService orderService;

@Inject
public CustomerController(CustomerService customerService, OrderService orderService) {
    this.customerService = customerService;
    this.orderService = orderService;
}

public void createCustomer() {
    customerService.addCustomer(); // This is safer as customerService is guaranteed to be initialized.
    orderService.placeOrder(); // This is safer as orderService is guaranteed to be initialized.
}
```

In this code sample, we can see that `createCustomer()` method can safely call methods on `customerService` and `orderService` without worrying about them being `null`. This is because they are guaranteed to be initialized when `CustomerController` is created.

For more insights on the benefits of constructor injection, you can refer to these sources:

- [Constructor Dependency Injection in Spring | Baeldung](https://www.baeldung.com/constructor-injection-in-spring)
- [java - Explain why constructor inject is better than other options ...](https://stackoverflow.com/questions/21218868/explain-why-constructor-inject-is-better-than-other-options)
- [Why You Should Use Constructor Injection in Spring - Reflectoring](https://reflectoring.io/constructor-injection/)
- [Understanding Constructor Injection - Manning](https://freecontent.manning.com/understanding-constructor-injection/)

## Dependency Injection Frameworks: The Bigger Picture

The Java ecosystem has seen a significant evolution in the landscape of Dependency Injection (DI) frameworks. Each framework has its unique features and conventions, serving different types of applications. Here's a brief overview of the popular DI frameworks in Java:

- [Spring](https://spring.io/): Spring is a comprehensive application framework for developing Java applications. Its DI container supports both field injection and constructor injection. Spring is widely used in enterprise applications due to its extensive feature set and integration with Java EE. However, it can be heavy for simple applications. 

- [Google Guice](https://github.com/google/guice): Guice is a lightweight DI framework developed by Google. It uses the `@Inject` annotation for dependency injection. Guice is known for its simplicity and flexibility, making it a good choice for applications that don't require the full feature set of Spring. However, it lacks some of the conveniences of Spring, such as auto-configuration and extensive integration with other technologies. You can read more about the comparison between Guice and Spring on [Baeldung](https://www.baeldung.com/guice-spring-dependency-injection).

- [Dagger](https://dagger.dev/): Dagger is a compile-time DI framework that is commonly used in Android development. It also uses the `@Inject` annotation. Dagger is known for its performance and efficiency, as it generates dependency injection code at compile time. However, it has a steeper learning curve and requires more boilerplate code compared to Spring and Guice. You can read more about Dagger in this [Medium article](https://medium.com/bigeye/dependency-injection-103-guice-vs-dagger-6ab7afa68d3d).

The `@Inject` annotation is part of the Java CDI (Contexts and Dependency Injection) specification, which is a standard for DI in Java. This means that it is not tied to a specific framework like Spring's `@Autowired` annotation. This makes `@Inject` more versatile and widely applicable across different DI frameworks.

Choosing `@Inject` over `@Autowired` makes your code more portable. If you decide to switch from Spring to another DI framework that supports the CDI standard, you won't have to change your injection annotations. This can save you a significant amount of time and effort, especially in large codebases.

# Embracing the Future: The Power of Constructor Injection

As we journey through the evolution of Java, we see how the language and its practices have matured over time. The shift from field injection to constructor injection is a testament to this growth. It's not just about writing code that works, but about writing code that is robust, testable, and maintainable.

The use of constructor injection, particularly with the `@Inject` annotation, has become a standard in modern Java development. It promotes loose coupling, simplifies testing, and ensures null safety. By making our dependencies final and injecting them through the constructor, we can guarantee that our objects are always in a valid state.

The `@Inject` annotation, preferred over `@Autowired`, further enhances this practice, offering a more standardized and flexible approach to dependency injection.

As we continue to explore the latest best practices in Java, let's embrace constructor injection and the `@Inject` annotation. They are not just tools, but powerful allies in writing clean, efficient, and reliable code.

In our next blog post, we'll delve into another exciting topic in modern Java development. Stay tuned and keep coding!
