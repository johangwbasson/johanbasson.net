---
title: "Java Record vs Lombok"
date: 2022-07-18T16:46:45+02:00
draft: false
hero: code.jpg
description: Java Records vs Lombok
---

What is [Lombok](https://projectlombok.org/)?

Lombok is a 3rd party library that enhances Java with custom annotations. These annotations are added to a class or fields at code time.

At compile time, Lombok will transform a class file auto-generating code behind the scenes to make the life of a developer easier.

Let's look at an example:

```java
@Data
class Person {
    private String firstName;
    private String lastName;
}
```

The @Data annotation of Lombok will generate getters, setters, equals, and hash and toString method. The effective class will look like this:

```java
@Data
class Person {
    private String firstName;
    private String lastName;

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

    public boolean equals(Object o) {
        ...
    }

    public int hashCode() {
        ...
    }

    public String toString() {
        ...
    }
}
```

What is a Java Record?

Java records were added to Java in version 14. The language has been enhanced with the "record" keyword to create immutable data structures.

Here is an example of the same Person:

```java
public record Person(String firstName, String lastName) {
}
```

The data structure consists of two fields: "firstName" and "lastName". In addition, the Java compiler will generate getter methods, equals() and hash() methods as well as toString(). The structure is immutable so there are no setter methods.

**So what is the difference?**

Both Lombok and Java records will generate the same getters, equals, hash, and toString methods for you. Records are great when you want an immutable data structure.

Lombok, on the other hand, gives you more out of the box: It has annotations for immutable data structure: [@Value](https://projectlombok.org/features/Value) but it can do more than just auto generating immutable classes. It also has the [@Builder](https://projectlombok.org/features/Builder) annotation which generates the builder pattern for you automatically.

Lombok gives you more flexibility out of the box with its various annotations.

I do like however that the Java language is progressing and that these types of enhancements are added to Java.

**When to use Lombok or Records?**

In my opinion, I would use records if you need immutable classes and your data structure has a few fields. I would use Lombok if you have a lot of fields, or when you need more than just immutability, for example using the builder annotation to construct an instance of the class especially when there are a lot of fields. 
