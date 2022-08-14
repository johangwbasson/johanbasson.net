---
title: "Functional Programming Principles"
date: 2022-07-12T15:31:03+02:00
draft: false
hero: mac-code.jpg
description: Function Programming Principles
---

Functional programming is a way to think about programs as a set of functions. It avoids concepts such as shared data and data mutation. By using Functional Programming principles we as developers can write better programs with much more stability. Let's look at a few principles.

## No side effects / Pure Functions

A function is considered pure when it does not have any side effects. A function has side effects when its input does not always guarantee the same output. Examples of this is any I/O that is performed using a database or file system or networking.

A Function is considered pure when the same input generates the same output, examples of this are calculations and non-I/O operations.

In Functional Programming, we aim to write pure functions as much as possible, but it is not always possible. Your application needs to interact with resources like databases or file systems or the network in order to be useful.

We want pure functions but I/O cannot be avoided. In these cases, we try to move I/O at the edge of the world, which is near the start of your program.

## Immutability

Immutability refers to a data structure or object whose values cannot change. If you want to change a value inside the structure, you need to create a new instance of the same data structure.

Immutability helps us to create programs that are safer and work better especially when it comes to multi-threading. Immutability ensures that a shared state is not updated or that multiple threads are not updating the same data structure or object.


## Referential transparency

An expression is referential transparent when the function can be replaced by its value, without changing the program's behavior. This requires that the expression is pure - that is, there are no side effects.

## Higher order functions

Higher order functions are functions that perform an operation on other functions. This means that a function can take one or more functions as parameters or return a function.

## Type systems

According to Wikipedia, the definition of Type Systems is:

"a type system is a logical system comprising a set of rules that assigns a property called a type to every "term", usually the various constructs of a computer program, such as variables, expressions, functions or modules. A type system dictates the operations that can be performed on a term and, for variables, the possible values it might be replaced with. Type systems formalize and enforce the otherwise implicit categories the programmer uses for algebraic data types, data structures, or other components (e.g. "string", "array of float", "function returning boolean").


What are examples of types?

Age, Book, Document, Name, etc. These would act as a type and would encapsulate the value and possibly expose functionality.

In the next post, I will show an example of these applied concepts applied to a Java application.
