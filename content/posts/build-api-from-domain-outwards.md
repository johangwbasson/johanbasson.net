---
title: "Building an API from the Domain Outwards"
date: 2026-07-05
draft: false
tags:
  - kotlin
  - architecture
  - functional-programming
  - api-design
  - clean-architecture
categories:
  - Software Development
---

When building backend services, it is very easy to start with the framework.

Create the controller.

Generate the database entity.

Add the repository.

Then somewhere between HTTP requests and database tables, try to find a place where the business logic fits.

I have built many systems this way.

It works.

Until it doesn't.

Over time I have started moving in the opposite direction:

Start with the domain.

Everything else is just an adapter.

## The Framework Is Not the Application

Frameworks are useful.

They solve real problems:

- routing
- serialization
- dependency injection
- configuration
- database access
- observability

But they should not define your application.

Your business problem existed before you selected a framework, and it will probably exist after that framework becomes outdated.

The domain should be the most stable part of your system.

## Start With the Use Case

Consider a simple authentication flow.

Most implementations start here:

```kotlin
@PostMapping("/login")
fun login(request: LoginRequest): ResponseEntity<Token>
```

The first decision has already been made.

The application is now shaped like the web framework.

Instead, what if we started with the actual problem?

```kotlin
fun authenticate(
    email: Email,
    password: PlainTextPassword
): Either<AuthError, Token>
```

No HTTP.

No JSON.

No database.

Just the business capability.

Dependencies Become Questions

Once the use case exists, it tells us what it needs.

Authentication needs to:

find a user
verify a password
create a token

Those become ports:

```kotlin
typealias FindUserByEmail =
    (Email) -> Either<AuthError, User>

typealias VerifyPassword =
    (PlainTextPassword, PasswordHash) -> Boolean

typealias GenerateToken =
    (User) -> Either<AuthError, Token>
```

The domain does not know where users come from.

PostgreSQL?

A REST service?

A test fixture?

It does not matter.

Infrastructure Implements Answers

At the edge of the application we connect reality.

```kotlin
val findUser =
    postgresUserRepository::findByEmail

val verifyPassword =
    bcryptPasswordVerifier::verify

val generateToken =
    jwtTokenGenerator::generate
```

The database is a detail.

JWT is a detail.

The domain remains unchanged.

HTTP Is Just Another Adapter

The API layer becomes translation.

Its job is simple:

HTTP → Domain → HTTP

```kotlin
fun loginHandler(request: Request): Response {
    val command = request.toDomain()

    return authenticate(command)
        .fold(
            { error -> error.toHttpResponse() },
            { token -> token.toHttpResponse() }
        )
}
```

The handler does not contain business rules.

It only connects the outside world to the inside.

Testing Becomes Easier

A side effect of this design is simpler tests.

You do not need:

a running server
a database container
mocked framework objects

You test behaviour.

```kotlin
authenticate(
    findUser = { validUser },
    verifyPassword = { true },
    generateToken = { token }
)
```

Fast tests encourage better design.

Better design encourages more tests.

Errors Are Part of the Domain

Exceptions are useful for unexpected failures.

But business failures are expected.

Invalid passwords happen.

Users do not exist.

Accounts get disabled.

Represent them:

```kotlin
sealed interface AuthError {

    data object InvalidCredentials : AuthError

    data object AccountDisabled : AuthError

    data class SystemFailure(
        val reason: String
    ) : AuthError
}
```

The compiler now understands your domain.

Architecture Is About Direction

The important rule is simple:

Dependencies point inward.

HTTP depends on the domain.

Database depends on the domain.

The domain depends on nothing.

The center should not know what is around it.

Conclusion

Good architecture is not about folders.

It is about protecting decisions.

Frameworks change.

Databases change.

Deployment platforms change.

Your business rules should survive all of them.

Build from the domain outwards.

Let everything else plug in.