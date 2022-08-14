---
title: "Using Apache Kafka as an Eventbus in Spring"
date: 2022-07-18T16:46:50+02:00
draft: false
hero: board.jpg
description: Using Apache Kafka as an Eventbus in Spring
---

At some point, you may need to build an application(s) that reacts to events. This makes your application scalable as well as allows different parts to react to some event.

An example of such a use case is user registration. When a user is registered in the system you might need to send out an email welcoming the user. There might be a business case to provision his workspace or some required data initialization for the user to be able to use the system.

We begin by defining the EventBus. Here we will code to an interface to allow for multiple implementations.

```java
public interface EventBus {

    void publish(Object event);

}
```

And here is the default implementation:

```java
@Component
@Slf4j
public class DefaultEventBus implements EventBus {

    private final Map<Class<?>, Set<HandlerMetaData>> handlerMapping = new HashMap<>();

    private record HandlerMetaData(
            Object object,
            Method method) {
    }

    public void register(Object obj) {
        for (Method method : obj.getClass().getMethods()) {
            if (hasHandlerAnnotation(method) && method.getParameterCount() == 1) {
                Class<?> clazz = method.getParameters()[0].getType();
                Set<HandlerMetaData> handlers = handlerMapping.computeIfAbsent(clazz, c -> new HashSet<>());
                handlers.add(new HandlerMetaData(obj, method));
                handlerMapping.remove(clazz);
                handlerMapping.put(clazz, handlers);
            }
        }
    }

    @SuppressWarnings("unchecked")
    public void publish(Event event) {
        Set<HandlerMetaData> handlers = handlerMapping.get(event.getClass());
        if (handlers == null) {
            throw new EventBusException("There is no handlers registered for " + event.getClass().getName());
        }
        handlers.forEach(md -> {
            try {
                md.method.invoke(md.object, event);
            } catch (Exception ex) {
                log.error("Error invoking event. Class: {}, Method: {}", md.object.getClass().getName(), md.method.getName());
            }
        });
    }

    private boolean hasHandlerAnnotation(Method method) {
        return method.getAnnotation(EventHandler.class) != null;
    }
}
```

We also need an Annotation to tell the EventBus about our handler:

The EventBus is wired in Spring as follows:

```java
@Bean
public EventBus eventBus(ApplicationContext context) {
    DefaultEventBus eventBus = new DefaultEventBus();
    context.getBeansWithAnnotation(Service.class)
            .forEach((s, o) -> eventBus.register(o));
    return eventBus;
}
```

To use Kafka we need an event dispatcher and receiver to send the event over the topic. The consumer will handle the event and use the EventBus to invoke each handler.

Here is the dispatcher:

```java
@Component
@AllArgsConstructor
public class KafkaEventDispatcher implements EventDispatcher {

    private final KafkaTemplate<String, net.johanbasson.kafka.eventbus.core.eventbus.Event> kafkaTemplate;

    @Override
    public void dispatch(Event event) {
        kafkaTemplate.send(KafkaTopics.EVENTS, event.getEventId().toString(), event);
    }
}
```

And consumer:

```java
@Component
@AllArgsConstructor
public class KafkaEventConsumer {

    private final EventBus eventBus;

    @KafkaListener(topics = KafkaTopics.EVENTS, groupId = "${spring.kafka.consumer.group-id}")
    public void consume(ConsumerRecord<String, Event> payload) {
        eventBus.publish(payload.value());
    }
}
```

This takes care of the infrastructure. The user registration will be a simple implementation to illustrate the usage case:

```java
@Service
@AllArgsConstructor
@Slf4j
public class UserService {

    private final UserRepository userRepository;

    @Autowired
    private final EventDispatcher eventDispatcher;


    public void register(RegisterUserCommand command) {
        Optional<User> maybeUser = userRepository.findByEmail(command.getEmail());
        if (maybeUser.isPresent()) {
            throw new ApiError("User already exists");
        }

        log.info("Command : {}", command);
        UserRegisteredEvent event = new UserRegisteredEvent(UUID.randomUUID(), command.getEmail(), command.getPassword(), LocalDateTime.now());
        eventDispatcher.dispatch(event);
        log.info("Dispatched UserRegisteredEvent: {}", event);
    }
}

```

We check if the user already exists. If the user exists let the user know by throwing an exception.

If he does not exists, publish the UserRegisteredEvent over Kafka using our dispatcher.
And finally, the consumer of the event will persist the user to the database:

```java
@Service
@AllArgsConstructor
@Slf4j
public class UserView {

    private final UserRepository userRepository;

    @EventHandler
    public void handle(UserRegisteredEvent event) {
        userRepository.insert(event.toUser());
        log.info("Inserted User {}", event);
    }
}
```

The code is available here: [https://github.com/johangwbasson/apache-kafka-eventbus-spring](https://github.com/johangwbasson/apache-kafka-eventbus-spring)
