+++
title = "Discovering Railway-Oriented Programming"
date = 2025-06-15T20:00:00+02:00

summary = "A talk by Scott Wlaschin changed the way I think about error handling and composing business logic in Kotlin."

tags = [
"Kotlin",
"Functional Programming",
"Railway Oriented Programming"
]
+++

A few years ago I came across a talk by Scott Wlaschin introducing **Railway-Oriented Programming**.

At first, I thought it was simply another functional programming technique.

It turned out to be much more than that.

It changed the way I think about business logic.

## The Problem

Like many Kotlin developers, I relied heavily on exceptions.

They worked.

But they also made it difficult to answer a simple question:

> What can this function actually return?

Sometimes it returned a value.

Sometimes it threw an exception.

Sometimes it did both.

The function signature didn't tell the whole story.

## A Different Way to Think

Scott Wlaschin introduced a simple but powerful idea.

Instead of throwing exceptions for expected business failures, treat success and failure as equally valid outcomes.

Every step in a workflow either continues successfully or returns a well-defined error.

That idea immediately resonated with me.

## Composition

What fascinated me wasn't just error handling.

It was composition.

Once every function follows the same pattern, complex workflows become easier to build from smaller pieces.

Authentication becomes a pipeline.

Validation becomes a pipeline.

Order processing becomes a pipeline.

Each step has one responsibility.

## Bringing the Idea to Kotlin

Kotlin isn't F#.

Nor should it be.

Instead of trying to copy Railway-Oriented Programming exactly, I started borrowing the ideas that made sense.

Around the same time, I discovered the **Arrow** library for Kotlin. Arrow provides functional programming primitives such as `Either`, which made it possible to model success and failure explicitly without relying on exceptions.

Using `Either` felt like a natural fit for the ideas Scott Wlaschin described. Instead of throwing expected business exceptions, I could return either a successful result or a well-defined domain error.

Over time these ideas influenced almost every use case I wrote.

## More Than Error Handling

Looking back, Railway-Oriented Programming taught me something much bigger.

It taught me that software becomes easier to understand when success and failure are visible.

Instead of asking:

> "What might this function throw?"

I can simply look at the return type.

The function tells me its story.

## Final Thoughts

Looking back, Railway-Oriented Programming gave me a new way to think about software design.

Scott Wlaschin's ideas introduced me to explicit error handling and function composition, while Arrow provided practical tools for applying those ideas in Kotlin.

I don't consider myself a pure functional programmer, nor do I believe every application should follow a strict functional style.

Instead, I've adopted the ideas that consistently make my code easier to understand:

* Explicit error handling
* Composable functions
* Small dependencies
* Immutable data where practical
* Clear business workflows

Today I still write ordinary Kotlin applications.

I simply approach them with a different mindset than I did a few years ago.

## Further Reading

If you're interested in learning more about Railway-Oriented Programming, I highly recommend Scott Wlaschin's original talk and accompanying article.

Scott has an exceptional ability to explain functional programming concepts in a practical and approachable way. His talks and the **F# for Fun and Profit** website have had a significant influence on how I think about software design.

* **Railway-Oriented Programming** – https://fsharpforfunandprofit.com/rop/
* **Scott Wlaschin – Railway-Oriented Programming (Video)** – https://www.youtube.com/watch?v=fYo3LN9Vf_M

Even if you don't write F#, the ideas are directly applicable to languages like Kotlin, Java and C#. They certainly changed the way I approach error handling and composition.
