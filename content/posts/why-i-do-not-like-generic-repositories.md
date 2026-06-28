+++
title = "Why I Don't Like Generic Repository Interfaces"
date = 2025-05-13T21:00:00+02:00
draft = false

summary = "I used to create a repository interface for every aggregate. Over time I found a simpler approach that makes dependencies more explicit and business use cases easier to understand."

tags = [
"Architecture",
"Kotlin",
"Hexagonal Architecture"
]
+++

When I first started using Hexagonal Architecture, I followed a pattern I'd seen many times.

Every aggregate had a repository.

```kotlin
interface UserRepository {
    fun findById(id: UserId): User?
    fun findByEmail(email: Email): User?
    fun save(user: User)
    fun delete(id: UserId)
}
```

It felt natural.

Eventually, I started asking a simple question:

> Does every use case really need all of these operations?

## The Problem

Imagine an authentication use case.

It needs exactly one database operation:

* Find a user by email.

That's it.

It doesn't need to save users.

It doesn't delete users.

It doesn't list users.

Yet the dependency suggests that all of those operations are available.

The interface communicates more than the use case actually requires.

## Depending on Behaviour

Instead of injecting a repository, I started depending on the behaviour the use case actually needs.

```kotlin
fun interface FindUserByEmail {
    operator fun invoke(email: Email): User?
}
```

Now the dependency tells a much clearer story.

When I open the authentication use case I immediately know what it needs.

Nothing more.

Nothing less.

## Smaller Building Blocks

The same idea applies throughout the application.

Instead of one large repository interface, I create small interfaces for individual operations.

```kotlin
fun interface SaveUser

fun interface DeleteUser

fun interface FindUserById
```

Each one has a single responsibility.

Each one can be implemented independently.

## Easier Testing

Testing also becomes simpler.

A unit test no longer needs to mock a repository containing methods that will never be called.

It only provides the behaviour required by the use case.

That tends to produce tests that are easier to read and maintain.

## It's Not About Repositories

This isn't really an argument against repositories.

Repositories work well in many applications.

The lesson I took away is broader than that.

Dependencies should communicate intent.

If a use case only needs one operation, then that's the dependency I want it to declare.

## Final Thoughts

Over time I've become less interested in creating reusable abstractions and more interested in making code communicate clearly.

Small function interfaces may result in more types, but they also make each dependency explicit.

When I open a use case today, I don't want to ask:

> "What can this dependency do?"

I want to immediately understand:

> "What does this use case need?"
