---
title: "Java Evolution: Builder Pattern Misuses and Modern Alternatives"
excerpt: "An exploration of common misuses of the Builder pattern in Java and how to avoid them with modern practices."
last_modified_at: 2023-06-01T03:12:00T-05:00
categories:
  - java
  - builder-pattern
  - modern-practices
tags: 
  - java
  - builder-pattern
  - java-records
  - mapstruct
  - factory-pattern
---
## Introduction

Welcome back to our Java Evolution series! In this installment, we're going to take a deep dive into the Builder pattern, a design pattern that has been a staple in Java for many years. The Builder pattern is a creational design pattern that separates the construction of a complex object from its representation[^1^]. This pattern is particularly useful when you need to create an object with many possible configuration options[^4^].

## The Traditional Builder Pattern in Java

The Builder pattern was introduced to solve some of the problems with Factory and Abstract Factory design patterns when the object contains a lot of attributes[^2^]. It's especially useful when designing classes whose constructors or static factories would have more than a handful of parameters[^1^]. 

Here's a traditional example of the Builder pattern in Java:

```java
public class Pizza {
    private int size;
    private boolean cheese;
    private boolean pepperoni;
    private boolean bacon;

    public static class Builder {
        //required
        private final int size;
        //optional
        private boolean cheese = false;
        private boolean pepperoni = false;
        private boolean bacon = false;

        public Builder(int size) {
            this.size = size;
        }

        public Builder cheese(boolean value) {
            cheese = value;
            return this;
        }

        public Builder pepperoni(boolean value) {
            pepperoni = value;
            return this;
        }

        public Builder bacon(boolean value) {
            bacon = value;
            return this;
        }

        public Pizza build() {
            return new Pizza(this);
        }
    }

    private Pizza(Builder builder) {
        size = builder.size;
        cheese = builder.cheese;
        pepperoni = builder.pepperoni;
        bacon = builder.bacon;
    }
}
```

This pattern allows you to produce different types and representations of a product using the same construction code[^4^].

Here's an updated version of the "Common Misuses of the Builder Pattern in Java" section with examples:

## Common Misuses of the Builder Pattern in Java

### Misuse 1: Overuse for Simple Objects

The builder pattern is intended for objects with complex construction, not for simple objects. Using it for simple objects can lead to unnecessary complexity and verbosity. 

Consider the following example:

```java
public class Person {
    private String name;
    private int age;

    public static class Builder {
        private String name;
        private int age;

        public Builder withName(String name) {
            this.name = name;
            return this;
        }

        public Builder withAge(int age) {
            this.age = age;
            return this;
        }

        public Person build() {
            Person person = new Person();
            person.name = this.name;
            person.age = this.age;
            return person;
        }
    }
}
```

In this case, the `Person` class is simple and doesn't require a builder. However, it's not uncommon to see builders used in such scenarios:

```java
Person person = new Person.Builder()
    .withName("John Doe")
    .withAge(30)
    .build();
```

This overuse of the builder pattern for simple objects can lead to bloated code that is harder to read and maintain.

### Misuse 2: Object Mapping

Another common misuse of the builder pattern is using it for object mapping, such as mapping API responses from an upstream service or database representations to the representation that needs to be returned by this API. 

Consider the following example:

```java
public class UserDto {
    private String username;
    private String email;

    // getters and setters omitted for brevity

    public static class Builder {
        private String username;
        private String email;

        public Builder withUsername(String username) {
            this.username = username;
            return this;
        }

        public Builder withEmail(String email) {
            this.email = email;
            return this;
        }

        public UserDto build(User user) {
            UserDto userDto = new UserDto();
            userDto.username = user.getUsername();
            userDto.email = user.getEmail();
            return userDto;
        }
    }
}
```

In this case, the builder is being used to map a `User` object to a `UserDto` object. This is not the intended use of the builder pattern. Here's how it might look in practice:

```java
User user = getUserFromDatabase(); // Assume this method fetches a User object from a database

UserDto userDto = new UserDto.Builder()
    .withUsername(user.getUsername())
    .withEmail(user.getEmail())
    .build();
```

This misuse of the builder pattern for object mapping can lead to confusion and can make the code harder to understand and maintain.

### Misuse 3: Complex Hierarchical Object Models

The builder pattern can become unwieldy and difficult to manage when used with complex hierarchical object models. 

Consider the following example:

```java
public class House {
    private Kitchen kitchen;
    private LivingRoom livingRoom;
    private List<Bedroom> bedrooms;

    // getters and setters omitted for brevity

    public static class Builder {
        private Kitchen kitchen;
        private LivingRoom livingRoom;
        private List<Bedroom> bedrooms = new ArrayList<>();

        public Builder withKitchen(Kitchen.Builder kitchenBuilder) {
            this.kitchen = kitchenBuilder.build();
            return this;
        }

        public Builder withLivingRoom(LivingRoom.Builder livingRoomBuilder) {
            this.livingRoom = livingRoomBuilder.build();
            return this;
        }

        public Builder addBedroom(Bedroom.Builder bedroomBuilder) {
            this.bedrooms.add(bedroomBuilder.build());
            return this;
        }

        public House build() {
            House house = new House();
            house.kitchen = this.kitchen;
            house.livingRoom = this.livingRoom;
            house.bedrooms = this.bedrooms;
            return house;
        }
    }
}
```

In this case, the `House` class has a complex structure with multiple nested builders. This is a common scenario in many codebases:

```java
House house = new House.Builder()
    .withKitchen(new Kitchen.Builder().withSize(20).withColor("White"))
    .withLivingRoom(new LivingRoom.Builder().withSize(30).withColor("Blue"))
    .addBedroom(new Bedroom.Builder().withSize(15).withColor("Green"))
    .addBedroom(new Bedroom.Builder().withSize(10).withColor("Yellow"))
    .build();
```

This misuse of the builder pattern for complex hierarchical object models can lead to verbose and hard-to-read code.

## Embracing Modern Practices: Alternatives to the Builder Pattern

Avoiding the misuse of the Builder Pattern in Java can be achieved by adopting modern features and practices. Here are some suggestions:

1. **Use Java Records for Simple Objects**: Java Records, introduced in Java 14, are a great way to create simple data carrier classes without the need for a builder. They automatically generate constructors, getters, equals(), hashCode(), and toString() methods, reducing boilerplate code. For example, instead of using a builder for a simple `Person` class, you could use a record:

    ```java
    public record Person(String name, int age) {}
    ```

    This creates a simple, immutable `Person` class with two fields: `name` and `age`. You can create a new `Person` instance like this:

    ```java
    Person person = new Person("John Doe", 30);
    ```

    This is much simpler and more concise than using a builder for such a simple class. You can read more about Java Records in the [Java 14 documentation](https://openjdk.java.net/jeps/359).

2. **Use Mapping Libraries for Object Mapping**: Instead of using a builder for object mapping, consider using a mapping library like [MapStruct](https://mapstruct.org/) or [ModelMapper](http://modelmapper.org/). These libraries provide powerful and flexible object mapping capabilities, and can handle complex mappings, conversions, and transformations.

3. **Simplify Complex Hierarchical Object Models**: If you find yourself using complex builders with many chained methods, consider simplifying your object model. This could involve breaking down complex objects into simpler ones, or using different design patterns such as the Factory or Prototype patterns. 

4. **Fluent API Pattern**: The Fluent API pattern can be used to enforce a particular sequence of calls and enable the call to the build() method only when all fields are set. This way, we shift all checks back to compile time. You can read more about this approach in this [DZone article](https://dzone.com/articles/why-builder-often-is-antipattern-and-how-to-replac).

5. **Use Libraries for Builder Generation**: Libraries like [FreeBuilder](https://github.com/google/FreeBuilder) or [Lombok](https://projectlombok.org/) can generate builder classes for you, reducing boilerplate code and potential errors. These libraries also provide additional features, such as support for optional parameters and automatic generation of equals(), hashCode(), and toString() methods.

Remember, the key is to use the right tool for the job. The builder pattern is a powerful tool, but like any tool, it can be misused. By understanding the pattern and its alternatives, you can write cleaner, more maintainable code.

## Applying Modern Practices to Avoid Misuses

Let's revisit the misuses we discussed earlier and apply modern practices to avoid them.

### Misuse 1: Overuse for Simple Objects

Instead of using a builder for a simple `Person` class, we can use a Java Record. This reduces the amount of boilerplate code and makes the code more readable:

```java
public record Person(String name, int age) {}
```

Creating a new `Person` instance is now straightforward:

```java
Person person = new Person("John Doe", 30);
```

### Misuse 2: Object Mapping

For object mapping, we can use a mapping library like MapStruct. This provides a more robust and flexible solution for object mapping. Here's an example of how you might use MapStruct to map a `User` object to a `UserDto` object:

```java
@Mapper
public interface UserMapper {
    UserMapper INSTANCE = Mappers.getMapper(UserMapper.class);

    @Mapping(source = "username", target = "username")
    @Mapping(source = "email", target = "email")
    UserDto userToUserDto(User user);
}
```

You can then use this mapper in your code like this:

```java
User user = getUserFromDatabase(); // Assume this method fetches a User object from a database

UserDto userDto = UserMapper.INSTANCE.userToUserDto(user);
```

However, if both `User` and `UserDto` are Java Records, you might not even need a mapper. Java Records can have constructors, so you can add a constructor to `User` that takes a `UserDto` as a parameter:

```java
public record User(String username, String email) {
    public User(UserDto userDto) {
        this(userDto.username(), userDto.email());
    }
}
```

With this constructor, you can create a `User` object from a `UserDto` object like this:

```java
UserDto userDto = getUserDtoFromDatabase(); // Assume this method fetches a UserDto object from a database

User user = new User(userDto);
```

This approach is simpler and more straightforward than using a builder or a mapper for object mapping.

### Misuse 3: Complex Hierarchical Object Models

For complex hierarchical object models, we can simplify the object model and use the Factory pattern. Let's say we have a `House` class that requires a `Kitchen`, a `LivingRoom`, and a list of `Bedroom` objects. Instead of using a complex builder with many chained methods, we can create a `HouseFactory` that simplifies the creation of a `House`:

```java
public class HouseFactory {
    public House createHouse(Kitchen kitchen, LivingRoom livingRoom, List<Bedroom> bedrooms) {
        return new House(kitchen, livingRoom, bedrooms);
    }
}
```

Creating a new `House` instance is now simpler and more straightforward:

```java
Kitchen kitchen = new Kitchen(20, "White");
LivingRoom livingRoom = new LivingRoom(30, "Blue");
List<Bedroom> bedrooms = List.of(new Bedroom(15, "Green"), new Bedroom(10, "Yellow"));

House house = new HouseFactory().createHouse(kitchen, livingRoom, bedrooms);
```

By applying these modern practices, we can avoid the common misuses of the builder pattern and write cleaner, more maintainable code.

## The Journey Forward: Embracing Modern Java Practices

As we've seen, the Builder pattern, while powerful, can be misused in ways that lead to bloated, hard-to-read, and hard-to-maintain code. However, by understanding these common misuses and embracing modern Java practices, we can avoid these pitfalls and write cleaner, more efficient code.

Java Records, mapping libraries, and design patterns like Factory are just some of the tools at our disposal. By using the right tool for the job, we can ensure that our code is not only functional, but also elegant and easy to understand.

In our Java Evolution series, we'll continue to explore how Java is evolving and how we can make the most of these changes. Stay tuned for more insights and tips on writing modern, effective Java code.

[^1^]: [When would you use the Builder Pattern? - Stack Overflow](https://stackoverflow.com/questions/328496/when-would-you-use-the-builder-pattern)
[^2^]: [Builder Design Pattern in Java | DigitalOcean](https://www.digitalocean.com/community/tutorials/builder-design-pattern-in-java)
[^3^]: [Exploring Joshua Bloch’s Builder design pattern in Java - Oracle Blogs](https://blogs.oracle.com/javamagazine/post/exploring-joshua-blochs-builder-design-pattern-in-java)
[^4^]: [Builder in Java / Design Patterns - refactoring.guru](https://refactoring.guru/design-patterns/builder/java/example)
