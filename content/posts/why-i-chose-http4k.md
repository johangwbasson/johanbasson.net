+++
title = "Why I Chose http4k Instead of Spring Boot"
date = 2026-06-20T11:30:00+02:00
draft = false
summary = "Why I chose http4k over Spring Boot for my latest Kotlin project, and the trade-offs I considered."

tags = [
  "Kotlin",
  "http4k",
  "Spring Boot",
  "Architecture"
]
+++

I've spent much of my career building backend services with Java and Kotlin. Like many developers in the JVM ecosystem, I've worked extensively with Spring Boot.

When I started my latest personal project, **Arventis**, I made a different choice. Instead of reaching for Spring Boot, I chose **http4k**.

This wasn't because I think Spring Boot is a bad framework. Quite the opposite—it's a mature, well-supported ecosystem that powers countless production systems.

My decision came down to a simple question:

> **How much framework do I actually need?**

## Looking for Simplicity

Over the years I've found myself valuing simplicity more and more.

I don't enjoy spending time understanding framework magic, lifecycle annotations or dependency injection containers. I prefer code that makes its dependencies explicit and behaves like ordinary Kotlin.

With http4k, an HTTP handler is just a function.

There is very little hidden behaviour, which makes the application easier to understand and debug.

## Explicit Dependencies

One aspect of http4k that immediately appealed to me was explicit dependency wiring.

Instead of relying on annotations and runtime dependency injection, I construct my application directly in Kotlin.

That means I can trace exactly how the application starts and how each dependency is created.

The entry point becomes documentation.

## Functional Composition

I've become increasingly interested in functional programming.

Not because I want everything to be purely functional, but because ideas such as immutable data, composition and explicit error handling often produce code that is easier to reason about.

http4k fits naturally with this style.

Request handlers are ordinary functions.

Filters compose naturally.

Testing becomes straightforward because there is very little framework state to manage.

## Smaller Applications Feel Smaller

Arventis is a personal project.

It doesn't need dozens of Spring starters, auto-configuration or a large ecosystem of annotations.

It needs:

* HTTP endpoints
* PostgreSQL
* Authentication
* Background jobs
* Configuration
* Logging

That's about it.

http4k provides the HTTP layer and lets me choose the remaining libraries myself.

## Trade-offs

Choosing http4k isn't free.

Spring Boot offers an enormous ecosystem.

Many common problems already have mature integrations, extensive documentation and large communities.

With http4k, you make more architectural decisions yourself.

For me, that's actually part of the appeal.

## Final Thoughts

I didn't choose http4k because it's better than Spring Boot.

I chose it because it better matches the kind of software I enjoy building.

As my career has progressed, I've found myself caring less about using the most popular framework and more about writing software that remains understandable months or years later.

For Arventis, http4k feels like the right tool.

Whether it remains the right tool as the project grows is something I'll discover over time.
