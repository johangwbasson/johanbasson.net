---
title: "Functional Principles applied to Java"
date: 2022-07-13T08:33:10+02:00
draft: false
hero: keyboard.jpg
description: Functional Programming principles applied to Java
---

Java is not a pure functional programming language, but we can apply the functional programming principles quite easily with it. Note the next example is my interpretation and implementation of applying functional programming principles.

### Example Use Case
We will build a simple endpoint that would perform an authentication of a user and if successful generate a JWT token for accessing our API. If the authentication is unsuccessful we will return an error.

We will be using a functional programming library named Vavr [https://www.vavr.io/](https://www.vavr.io/). For the server, we'll be using Javalin [https://javalin.io/](https://javalin.io/).

Here is a bare minimum server:

```java
import io.javalin.Javalin;

public class Main {
    public static void main(String[] args) {  
        Javalin app = Javalin.create(cfg -> {  
            cfg.enableDevLogging();  
            cfg.enableCorsForAllOrigins();  
        });  
        app.start(8080);
     } 
} 
```

The request type will look like this. Note we will be using Lombok's @Value annotation to make it immutable.

```java
import lombok.Value;

@Value  
public class AuthenticateRequest {  
    String email;  
    String password;  
}
```

Or we can utilize Java's records to create an immutable data structure:

```java
public record AuthenticateRequest(String email, String password) {}
```

We also need a Token type class for data transfer:

```java
public record JwtToken(String token) {}
```

### Business logic

Here is the authentication business logic:

```java
public final class Authenticate {  

    public static Reader<Resources, Either<ApiError, Token>> authenticate(AuthenticateRequest request) {  
        return Reader.of(ctx ->  
                validate(cmd)  
                        .flatMap(findUser(ctx))  
                        .flatMap(Authenticate::checkPassword)  
                        .flatMap(ar -> right(Jwt.generateToken(ar.user).apply(ctx))));  
    }  
}
```

There is a lot to unpack here so let's look at the function:

The Reader gives us Resources as input and as Output, we use the Either monad. The Either monad has a left side and right side. It is a convention that the left side indicates an error while the right side indicates a success state.

So in this case the error state is an ApiError and the success state is a Token.

The authenticate method takes one parameter and that is the AuthenticateRequest. This request object we will construct from the incoming HTTP request.

The method body consists of the call to the Reader's method which gives us access to the Resources instance.

The subsequent calls are the heart of the business logic. We will first validate the request, then look up the user from the database. The user's password is validated and if successful we generate the token.

### Reader Monad

We use the Reader monad taken from: [https://github.com/enelson/java_monads/blob/master/src/main/java/com/enelson/monads/Reader.java](https://github.com/enelson/java_monads/blob/master/src/main/java/com/enelson/monads/Reader.java).

The Reader monad gives us a way to do dependency injection in a functional way.

### Resources Class

The Resources class is an immutable object which contains the dependencies required to operate. In this case, we need the UserRepository dependency to look up the user from the database.

Here are the classes for Resources and UserRepository:

```java
import lombok.Value;  

public record Resources(UserRepository userRepository) {
}
```

The Resources is used as a data structure for all the I/O bound elements in your application. It would typically contain your data source, repositories and additional services needed.

```java
public interface UserRepository {  
  Try<Option<User>> findByEmail(Email email);
}
```

We make use of the [Try](https://docs.vavr.io/#_try) monad from Vavr as we do not want to throw any exception up the stack, but let the caller know that the operation failed.

Here is the definition of Try according to Vavr:

"Try is a monadic container type that represents a computation that may either result in an exception or return a successfully computed value. It’s similar to, but semantically different from Either. Instances of Try, are either an instance of Success or Failure."

The findByEmail method takes an Email value object and returns a Try with an optional User. The Try monad wraps the function in a try-catch. The lookup to the database can fail at any point and we should cater for it by using the Try monad. We also get rid of exceptions in the method signature, getting rid of the "goto" which is the exception thrown and caught higher up the stack.

### Validation

Here is the validation method:

```java
private static Either<ApiError, ValidAuthenticateRequest> validate(AuthenticateRequest cmd) {  
    Validation<ApiError, Email> email = Validations.email(cmd.email());  
    Validation<ApiError, Password> password = Validations.password(cmd.password());  
    return Validation.combine(email, password)  
            .ap(ValidAuthenticateRequest::new)  
            .toEither()  
            .mapLeft(ApiError::validationErrors);  
}
```

Here we utilize Vavr's validation [API](https://docs.vavr.io/#_validation) to validate the AuthenticateRequest and convert the email and password strings to proper types (Email, Password).

We return a Either here as a response to indicate success or failure.

### Lookup user from database

Here is the 'findUser' method:

```java
private static Function<ValidAuthenticateRequest, Either<ApiError, UserAndRequest>> findUser(Resources ctx) {  
    return validAuthenticateRequest -> ctx.repositories().userRepository()  
            .findByEmail(validAuthenticateRequest.email())  
            .toEither()  
            .mapLeft(ApiError::databaseError)  
            .flatMap(maybeUser -> maybeUser.toEither(ApiError.error("User not found")))
            .map(user -> new UserAndRequest(user, validAuthenticateRequest));
}
```

This method is fairly straightforward, we call the findByEmail method, convert the Try to an Either, and map the exception to an ApiError (database error). If the user was not found, return an ApiError otherwise return the user and request as one immutable value.

### Verify user password

Here is the 'checkPassword' function:

```java
private static Either<ApiError, UserAndRequest> checkPassword(UserAndRequest t) {  
    if (t.user().password().matches(t.request().password())) {  
        return right(t);  
    } else {  
        return left(ApiError.error("Incorrect password"));  
    }  
}
```

### Generate token

And lastly the 'generateToken' function:

```java
public static Reader<Resources, JwtToken> generateToken(User user) {  
    return Reader.of(ctx -> {  
        LocalDateTime now = LocalDateTime.now();  
        LocalDateTime expires = now.plus(1, ChronoUnit.DAYS);  
        String token = Jwts.builder()  
                .setSubject(user.id().toString())  
                .claim(ROLE, user.role())  
                .setExpiration(Date.from(expires.toInstant(ZoneOffset.UTC)))  
                .signWith(ctx.security().secretKey(), SignatureAlgorithm.HS512)  
                .compact();  
        return new JwtToken(token, expires.atZone(ZoneId.systemDefault()).toInstant().toEpochMilli());  
    });  
}
```

This function utilizes the 'jsonwebtoken' [https://jwt.io/](https://jwt.io/).

The last part is to connect the server with the business function. This is where we have to unfortunately go back to the imperative way as I have not found a good server-side library that does HTTP in a functional manner (Although Spring Cloud Functions look promising). But for our purposes, it will suffice.

### Controller Class

```java
@AllArgsConstructor
public final class SecurityController {  

    private final Resources resources;  

    public void authenticate(Context ctx) {  
       Authenticate.authenticate(getAuthenticateRequest(ctx))
                   .apply(resources))
                   .fold(
                       err -> context.json(new ErrorResponse(apiError.getErrors())).status(HttpStatus.BAD_REQUEST_400),  
                       token -> context.json(payload).status(HttpStatus.OK_200)
                   );
    }
}
```

The controller class is where we link the Rest API to our business function.

### Wire in controller

And wiring in the controller:

```java
import io.javalin.Javalin;  

public final class Main {  

    public static void main(String[] args) {  
        var resources = new Resources();
        var securityController = new SecurityController(resources);  
        Javalin app = Javalin.create(cfg -> {  
            cfg.enableDevLogging();  
            cfg.enableCorsForAllOrigins();  
        });

        app.routes(() -> {
            path("/api/v1", () -> {
                post("/authenticate", securityController::authenticate);
            });
        });
        app.start(8080);  
    }  
}
```

### Testing our application

We have just implemented our business logic as a function, which helps quite a lot with testing it. We can now only test this function and be confident it works. Of course, the API also needs to be tested, for this post we will focus on testing the function. We will be using Junit with AssertJ to write our tests.

Here is the complete test:

```java
class AuthenticateTest {  

    private static final Resources resources = TestResourcesFactory.create();  

    @Test  
    void shouldAuthenticateWithCorrectCredentials() {  
        // Given  
        AuthenticateRequest cmd = new AuthenticateRequest("admin@local.com", "admin");  

        // When  
        Either<ApiError, JwtToken> result = Authenticate.authenticate(cmd).apply(io);  

        // Then  
        assertThat(result).isNotNull();  
        assertThat(result.isRight()).isTrue();  
        assertThat(result.get()).isNotNull();  
        assertThat(result.get().token()).isNotNull().hasSizeGreaterThan(200);  
    }  

    @ParameterizedTest  
    @MethodSource("provideArgumentsForAuthenticateFailures")  
    void shouldReturnError(AuthenticateRequest cmd, String errorMessage) {  
        Either<ApiError, JwtToken> result = Authenticate.authenticate(cmd).apply(io);  

        // Then  
        assertThat(result).isNotNull();  
        assertThat(result.isLeft()).isTrue();  
        assertThat(result.swap().get()).isNotNull();  
        assertThat(result.swap().get().toString()).hasToString(errorMessage);  
    }  

    private static Stream<Arguments> provideArgumentsForAuthenticateFailures() {  
        return Stream.of(  
                Arguments.of(new AuthenticateRequest("", "Pass1234"), "Invalid email specified"),  
                Arguments.of(new AuthenticateRequest("admin@local.com", ""), "Password cannot be empty"),  
                Arguments.of(new AuthenticateRequest("admin@abc.com", "Pass1234"), "User not found"),  
                Arguments.of(new AuthenticateRequest("a@a.com", "Pass1234"), "User not found"),  
                Arguments.of(new AuthenticateRequest("admin@local.com", "password"), "Incorrect password")  
        );  
    }  

}
```

And the TestResourceFactory:

```java
public class TestResourcesFactory {  
    private static final SecretKey SECRET_KEY = Keys.secretKeyFor(SignatureAlgorithm.HS512);  

    public static Resources create() {          
        var userRepository = new TestUserRepository();  
        var repositories = new Repositories(userRepository);  
        var security = new Security(SECRET_KEY);
        return new Resources(repositories, security);  
    }  
}
```

And TestUserRepository:

```java
public class TestUserRepository implements UserRepository {  
    private Set<User> users = HashSet.of(  
            new User(  
                    new Id(),  
                    new Email("admin@local.com"),  
                    PasswordHash.from("admin"),  
                    Role.ADMINISTRATOR,  
                    LocalDateTime.now(),  
                    LocalDateTime.now())  
    );

    @Override  
    public Try<Option<User>> findByEmail(Email email) {  
        return Try.of(() -> users.find(user -> user.email().equals(email)));  
    }
}
```

Testing comes super easy with this approach as you can create abstracts of the IO in an in-memory representation.

This has been quite a lot to take in. The project is available here: [https://github.com/johangwbasson/fp-in-java](https://github.com/johangwbasson/fp-in-java)

Till next time.

