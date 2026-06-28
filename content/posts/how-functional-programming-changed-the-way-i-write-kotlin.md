+++
title = "How Functional Programming Changed the Way I Write Kotlin"
date = 2025-06-10T20:00:00+02:00

summary = "Functional programming didn't change the language I use. It changed the way I think about designing software."

tags = [
"Kotlin",
"Functional Programming",
"Architecture"
]
+++

When people hear *functional programming*, they often think of unfamiliar languages, complex mathematical concepts or code that's difficult to read.

That wasn't my experience.

Functional programming didn't replace the way I wrote software.

Instead, it gradually changed the way I thought about software design.

## It Started with a Question

I wasn't trying to become a functional programmer.

I was trying to answer a much simpler question:

> How can I write software that's easier to understand and easier to change?

As I explored ideas from functional programming, I realised many of them solved problems I encountered every day in object-oriented applications.

## Explicit Errors

One of the biggest changes was how I handled errors.

For years I relied heavily on exceptions.

Eventually I found myself asking:

> What if errors were simply another possible result?

Instead of hiding failure inside exceptions, I started making it explicit in function signatures.

That simple shift made it much easier to understand what a piece of code could actually do.

## Small Composable Functions

Another lesson was composition.

Rather than building large classes with dozens of methods, I began writing smaller functions that each had a single responsibility.

Small pieces are easier to test.

They're easier to understand.

Most importantly, they're easier to combine in different ways.

## Immutable Data

Whenever possible, I now prefer immutable data.

Objects that don't change are easier to reason about because they can't unexpectedly be modified somewhere else in the system.

It reduces the number of things I need to keep in my head while reading code.

## Dependencies Become Clearer

Functional programming also influenced how I think about dependencies.

Instead of injecting large service objects, I increasingly depend on small pieces of behaviour.

A use case should declare exactly what it needs—nothing more.

That makes the code easier to read and makes testing much simpler.

## Functional Programming Isn't a Goal

I don't try to make every application purely functional.

Kotlin is a pragmatic language, and I appreciate that.

Instead, I borrow ideas that improve clarity:

* Explicit error handling
* Small composable functions
* Immutable data where practical
* Clear dependencies
* Simplicity over cleverness

## Final Thoughts

Functional programming didn't make me stop writing object-oriented software.

It simply gave me better tools for thinking about complexity.

Today I still write Kotlin.

I still build backend systems.

The difference is that I spend less time asking:

> "How should I structure these classes?"

and more time asking:

> "How can I make this code easier for the next engineer to understand?"
