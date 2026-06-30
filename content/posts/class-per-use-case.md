+++
title = "Class Per Use Case: A Simpler Way to Structure Applications"
date = 2026-06-30T18:42:00+02:00
draft = false

summary = "One of the principles that has shaped how I write software over the years is that code should explain itself."

tags = [
"Architecture",
"Software Engineering"
]
+++

When people talk about application architecture, the conversation often turns to frameworks, dependency injection, or hexagonal architecture. Those topics are important, but they don't answer a simpler question:

> **Where does the business logic actually live?**

After experimenting with several approaches over the years, I've settled on a pattern that keeps applications easy to navigate and maintain:

**One class represents one use case.**

Instead of large service classes that contain dozens of unrelated methods, every business operation gets its own class.

For example:

```
AuthenticateAccountUseCase
CreateWorkspaceUseCase
InviteMemberUseCase
ResetPasswordUseCase
UpdateProfileUseCase
```

Each class has exactly one responsibility.

---

## The Problem with Service Classes

Many applications eventually grow classes like this:

```kotlin
class AccountService {
    fun authenticate(...)
    fun register(...)
    fun changePassword(...)
    fun resetPassword(...)
    fun verifyEmail(...)
    fun resendVerification(...)
    fun deactivate(...)
    fun reactivate(...)
}
```

At first this seems reasonable.

Months later the class contains thousands of lines of code.

Every new feature means opening the same file again.

Testing becomes more complicated because the class depends on many collaborators that individual methods don't actually use.

The class slowly becomes the dumping ground for anything related to accounts.

This violates one of the most valuable design principles:

> A class should have one reason to change.

---

## Thinking in Use Cases

Instead of organising code around entities, organise it around business operations.

Each operation becomes a small, focused class.

```kotlin
class AuthenticateAccountUseCase(...)
```

```kotlin
class ChangePasswordUseCase(...)
```

```kotlin
class CreateWorkspaceUseCase(...)
```

Finding code becomes almost trivial.

Need to understand authentication?

Open `AuthenticateAccountUseCase`.

Need to understand workspace creation?

Open `CreateWorkspaceUseCase`.

There is no hunting through hundreds of methods.

---

## Commands In, Results Out

I prefer every use case to expose a single function.

```kotlin
operator fun invoke(command: AuthenticateAccountCommand)
```

The input is always a command object.

Commands describe **what** the caller wants to accomplish rather than how it should happen.

For example:

```kotlin
AuthenticateAccountCommand(
    email,
    password
)
```

Likewise, every use case returns a result object.

```kotlin
AuthenticateAccountResult(
    accessToken,
    refreshToken,
    expiresAt
)
```

This makes the API consistent across the application.

Every use case follows the same pattern.

* Command in
* Result out

---

## Dependencies Stay Small

Another advantage is that every class only depends on what it actually needs.

Authentication might require:

* Account repository
* Password verifier
* Token generator
* Clock

Nothing else.

Creating a workspace might require an entirely different set of dependencies.

Instead of constructing one massive service with fifteen dependencies, each use case stays small and focused.

This also makes testing straightforward.

Every dependency is relevant.

Nothing exists "just because another method needs it."

---

## Testing Becomes Obvious

Testing mirrors the structure of the application.

```
AuthenticateAccountUseCaseTest
CreateWorkspaceUseCaseTest
InviteMemberUseCaseTest
```

Each test suite focuses on exactly one business capability.

There is no need to understand unrelated functionality before making changes.

---

## Fits Naturally with Hexagonal Architecture

This approach works especially well with Ports and Adapters.

The use case sits in the application layer.

It coordinates domain objects through ports without knowing anything about HTTP, databases, or messaging systems.

A typical flow looks like this:

```
HTTP Handler
      │
      ▼
AuthenticateAccountUseCase
      │
      ▼
Application Ports
      │
      ▼
Infrastructure Adapters
```

Each layer has a clear responsibility.

---

## Naming Matters

Consistency is important.

I use a simple naming convention:

```
AuthenticateAccountUseCase
AuthenticateAccountCommand
AuthenticateAccountResult
```

Every feature follows the same pattern.

After a while, navigating the codebase becomes almost mechanical because every use case looks familiar.

---

## Benefits I've Seen

Since adopting this approach I've noticed several improvements:

* Smaller classes.
* Better separation of responsibilities.
* Simpler dependency graphs.
* Faster navigation through the codebase.
* Easier testing.
* Consistent APIs.
* Features remain isolated from one another.

Perhaps the biggest benefit is cognitive load.

When I open a use case, I know I'm looking at exactly one business operation.

There are no surprises.

---

## Final Thoughts

This pattern isn't revolutionary.

It's simply an application of the Single Responsibility Principle at the use-case level.

Every business capability gets its own class.

Every class has one purpose.

Combined with commands, results, and ports, this has produced applications that are easier to understand, easier to test, and easier to evolve over time.

Sometimes the biggest improvements don't come from adopting a new framework.

They come from choosing a simpler way to organise code.
