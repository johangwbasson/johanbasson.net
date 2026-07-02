---
title: "Railway-Oriented Programming Without the Magic"
subtitle: "How functional error handling can simplify your Kotlin business logic"
description: "Learn how Railway-Oriented Programming (ROP) and Arrow's Either type can make Kotlin backend applications easier to read, test, and maintain by making success and failure explicit."
summary: "A practical introduction to Railway-Oriented Programming in Kotlin using Arrow's Either. Learn how to replace exception-driven business logic with explicit, composable error handling."
date: 2026-07-02T08:00:00+02:00
lastmod: 2026-07-02T08:00:00+02:00
draft: false

authors:
  - Johan

categories:
  - Kotlin
  - Software Architecture

tags:
  - kotlin
  - arrow
  - functional-programming
  - railway-oriented-programming
  - error-handling
  - backend
  - architecture
  - hexagonal-architecture

keywords:
  - Railway Oriented Programming
  - ROP
  - Kotlin Either
  - Arrow KT
  - Functional Error Handling
  - Kotlin Backend
  - Hexagonal Architecture

toc: true
readingTime: true
showDate: true
showAuthor: true
showBreadcrumbs: true

series:
  - Functional Kotlin

cover:
  image: "cover.webp"
  alt: "Railway-Oriented Programming illustrated as two railway tracks representing success and failure."
  caption: "Making success and failure explicit."

---

> **How functional error handling can simplify your Kotlin business logic**

## Introduction

For years I wrote backend applications the same way many Java developers do.

A service method would call another service, which called a repository, which might throw an exception. Business validation was mixed with technical failures, and somewhere near the controller everything was wrapped in a giant `try/catch`.

It worked.

Until the business logic became complicated.

Authentication, account creation, password resets, permissions, transactions, auditing—every new feature added another set of exceptional cases that weren't really exceptional.

Eventually I stopped asking, *"What exception should this throw?"* and started asking a different question:

> What if failure was simply another valid result?

That's where Railway-Oriented Programming (ROP) changed how I structure backend applications.

This article isn't about category theory or monads.

It's about writing business code that's easier to read, easier to test, and harder to get wrong.

---

## The Problem With Exceptions

Consider a typical authentication service.

```kotlin
fun authenticate(email: String, password: String): Token {
    val account = accountRepository.findByEmail(email)
        ?: throw AccountNotFoundException()

    if (!passwordEncoder.matches(password, account.password)) {
        throw InvalidPasswordException()
    }

    if (!account.enabled) {
        throw AccountDisabledException()
    }

    return tokenService.create(account)
}
```

At first glance this looks reasonable.

But there are several hidden problems:

- Which exceptions are expected?
- Which indicate programming bugs?
- Which should become HTTP 400?
- Which should become HTTP 401?
- Which should become HTTP 500?

Nothing in the method signature tells us.

The only way to understand the possible outcomes is to read the implementation—or worse, inspect everything it calls.

---

## Making Failure Explicit

Instead of throwing exceptions, return either a failure or a success.

```kotlin
Either<AuthenticationError, AccessToken>
```

Now the function signature documents every possible outcome.

```kotlin
fun authenticate(
    command: AuthenticateCommand
): Either<AuthenticationError, AccessToken>
```

Immediately you know:

- Authentication can fail.
- Success returns an access token.
- Callers must handle both cases.

The compiler helps enforce this.

---

## Building a Railway

Railway-Oriented Programming gets its name from imagining two tracks.

One track represents success.

The other represents failure.

Every operation either continues on the success track or switches permanently to the failure track.

```text
Start
  │
Find Account
  │
Verify Password
  │
Check Status
  │
Create Session
  │
Generate Token
  │
Success
```

If any step fails, execution immediately switches to the failure track and the remaining operations are skipped.

No deeply nested `if` statements.

No exception propagation.

No giant `try/catch` blocks.

---

## Kotlin Makes This Surprisingly Elegant

Using Arrow, the authentication flow becomes almost declarative.

```kotlin
either {
    val account = findAccount(command.email).bind()

    ensure(
        verifyPassword(command.password, account)
    ) {
        InvalidCredentials
    }

    ensure(account.enabled) {
        AccountDisabled
    }

    createSession(account).bind()

    generateToken(account).bind()
}
```

Read it from top to bottom.

Each line represents a business rule.

If something fails, execution stops immediately.

No boilerplate.

Just business logic.

---

## Business Errors vs Technical Errors

One lesson I learned is that not all failures are equal.

Business failures are expected outcomes.

Examples include:

- Invalid password
- Email already exists
- Workspace not found

Technical failures are unexpected.

Examples include:

- Database unavailable
- Network timeout
- Disk full

Treating them separately keeps the domain clean.

```kotlin
sealed interface BusinessError

sealed interface TechnicalError
```

Repositories convert infrastructure exceptions into technical errors.

Use cases work only with domain concepts.

Controllers translate errors into HTTP responses.

Each layer has a single responsibility.

---

## Transactions Become Simpler

Traditional transactions rely on exceptions to trigger rollbacks.

With `Either`, transactions simply observe the result.

```kotlin
transaction {
    createAccount(command).bind()

    createAuditEntry().bind()

    publishEvent().bind()
}
```

If any step returns a failure, the transaction rolls back.

No exception-driven control flow is required.

---

## Testing Gets Easier

Instead of asserting exceptions...

```kotlin
assertThrows<AccountNotFoundException> {
    service.authenticate(command)
}
```

...you simply verify the returned value.

```kotlin
authenticate(command) shouldBe
    AccountNotFound.left()
```

No special testing constructs.

Just data.

---

## When Should You Still Throw Exceptions?

Railway-Oriented Programming doesn't eliminate exceptions.

It changes where they're used.

Exceptions remain appropriate for:

- Programming bugs
- Invalid application state
- Corrupted data
- Impossible situations

Business rules should usually return values.

Programmer mistakes should still fail fast.

---

## Final Thoughts

Railway-Oriented Programming isn't about writing "functional" code.

It's about making success and failure explicit.

Your business logic becomes a sequence of clearly defined steps.

The compiler helps ensure every failure is handled.

Testing becomes straightforward.

And the code starts reading like the business process it's implementing.

For me, that has been the biggest benefit.

Not fewer lines of code.

Clearer code.