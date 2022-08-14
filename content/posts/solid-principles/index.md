---
title: "SOLID Principles"
date: 2022-07-09T12:57:03+02:00
hero: solid.jpg
description: Five principles for Object Oriented class design
draft: false
tags: ["OOP", "Java"]
categories: ["Java"]
---

Software development is hard and there is a lot to know and understand in order to be a good developer. When it comes to Object Oriented Programming which is central to languages like Java and .Net, it becomes harder to build features using OOP, as you need to build everything using classes, objects, and properties.

We need to design classes, objects, and properties in a way which are easy to understand, easy to reason about, and above all, easy to maintain. This is where the SOLID principles come in. It is five principles that help you as a developer design and implement better code and more manageable code.

## So what is SOLID principles?

SOLID is an abbreviation of five principles which can be summarized as follows:

- S - Single Responsibility.
- O - Open for extension closed for modification.
- L - Liskov substitution principle - An object and its sub-object must be interchangeable.
- I - Interface Segregation Principle - Many client interfaces are better than one general purpose interface.
- D - Dependency Inversion - Depend on abstractions, not concretions.

Let's look at them each in detail:

## Single Responsibility

This principle is quite easy to understand but in practice, it sometimes gets overlooked quite easily. The principle states that a class or objects should have one responsibility (function) and it should only perform or be able to do this one function.

Why? Because understanding and maintaining a class with a single responsibility is much easier than one class having multiple functions and responsibilities.

What is a practical example of this?

```java
interface Parser {

    <T> T parse(String s, Class<T> type);

}
```

This interface has a single responsibility, to parse the string and produce the object of type T. It is easy to reason about and maintain.

The next example is a bad example of Single Responsibility:

```java
interface Person {
    void write(File f);
    void greet();
    Person read(File f);
}
```

Here the interface does three things:

- Write the person to a file
- The person class exposes a function called greet
- Reads a person from a file

In this case, it is hard to reason about the Person class as it does not have a single responsibility.

## Open for extension, close for modification

A class is open for extension when it can be used as a parent class and additional properties and functions can be added to the new class, without modifying the original class.

The same parent class is also closed for modification as the internal state should not be changeable.

Why is this important? Changes to the class (in this case the parent class) might need some other changes in other parts of the program. By extending the class and adding the required functionality, the original class does not need to change, but new functionality can be added to the new class.

This makes maintaining existing functionality much easier. Say you have to fix a bug in a class but you do not have the original code (it comes from a library). By extending the original class and making the necessary changes to fix the bug in the new class you have effectively fixed the bug and you could do it without the original code for the class!

## Liskov substitution principle

Say you have a class A which depends on class B as follows:

```java
class A {
    private final B b;

    public A(B b) {
        This.b = b;
    }
}
```

The Liskov substitution principle states that B can be substituted by its sub-class. So if I extend B with class C as follows:

```java
class C extends B {
}
```

Then I can pass an instance of class C instead of an instance of class B when I construct A:

```java
A a = new A(new C());
```

This principle helps us to build extensible classes without the need to change all occurrences of B.

## Interface Segregation Principle

This principle states that we should not use large interface(s), but we should aspire to use smaller interfaces. Why? The smaller the interfaces the more focused and specific the functionality of an interface is and the less the client has to know about it.

Here is an example: 

I have a Store interface:

```java
interface Store {

    void write(...);
    void read(...);

}
```

This interface can be split up even further to make it more specific (and more in line with single responsibility) as follows:

```java
interface Writer {
    void write(...);
}

interface Reader {
    void read(...);
}
```

Now a client class can use the interface which is needed:

```java
class ClientReader implements Reader {
    void read(...) {        
    }
}
```

And one can implement a specialization class for Reader:

```java
class ClientWriter implements Writer {

    void write(...) {

    }
}
```

## Dependency Inversion

We should strive to depend on abstractions instead of concretions. Why? This helps us build software which are not tightly coupled and which can be changed quite easily. If your class depends on concrete implementation, then it would be hard to change over time. Depending on an abstract class or interface ensures that we can change the class in the future and makes our lives as developers much easier.

## Conclusion

How do these principles help me as a developer?

It helps you by building better software in the longer run, by utilizing these principles software is much easier to reason about and to maintain. These principles makes it easier to enhance and build new features in the longer run.
