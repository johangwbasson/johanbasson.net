+++
title = "Code Should Read Like Documentation"
date = 2025-06-24T20:00:00+02:00
draft = false

summary = "One of the principles that has shaped how I write software over the years is that code should explain itself."

tags = [
"Architecture",
"Software Engineering"
]
+++

One of the principles that has shaped how I write software over the years is simple:

> **Code should read like documentation.**

That doesn't mean comments are bad. It means that someone reading the code should be able to understand what it does without constantly referring to documentation, diagrams or wiki pages.

## Software Changes

The biggest difference between writing software and writing a book is that software never stays finished.

Requirements change.

Features evolve.

Teams grow.

The code that made perfect sense six months ago often becomes difficult to understand because it has accumulated complexity.

When that happens, the cost of change increases.

## Reading Is More Common Than Writing

Most lines of code are read many more times than they are written.

Future-you will read the code.

Your teammates will read the code.

Someone debugging a production issue at two in the morning will read the code.

Optimising for readability often pays far greater dividends than optimising for brevity.

## Small Functions

I prefer functions that do one thing.

Not because it's a rule, but because smaller functions usually communicate intent more clearly.

Instead of asking *how* the code works, the reader should quickly understand *what* the code is trying to achieve.

## Good Names Matter

Naming is one of the most difficult parts of software engineering.

A well-named function or type can eliminate the need for several comments.

When I struggle to name something, it's often a sign that I haven't fully understood the responsibility it should have.

## Simplicity Is a Feature

Complex code isn't impressive.

Simple code is.

When software is easy to read, it's easier to review, easier to test and easier to change.

That doesn't happen by accident. It requires continuously asking whether a solution can be made a little simpler.

## Final Thoughts

I've come to believe that code isn't just instructions for a computer.

It's also communication between engineers.

If someone can understand what my code is doing without me standing beside them to explain it, then I've probably made a good design decision.
