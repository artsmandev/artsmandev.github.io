---
title: Lombok and IoC Take Care
description: Don't get influenced by Lombok's hidden things, keep strong on basics
tags: [Inversion of Control, Dependency Injection, Lombok]
---

Happy new year _++2025_, fellows! 🎊

Another real world `lombok's` adventure.
Today, I'm going to show a pice of how combine Lombok and IoC principle can be trick.

Let's look at this piece of code below:
```java
@RequiredArgsConstructor
public class OrderController {
  private final OrderService orderService;

  @Value("${polling-interval-in-seconds}")
  private int pollingIntervalInSeconds;

  @Value("${timeout-in-seconds}")
  private int timeoutInSeconds;
}
```

At first, it's a fancy code, right?
We're injecting `orderService`, `pollingIntervalInSeconds` and `timeoutInSeconds` successully.
What's the point here? _(you probably will be wondering by now)_
I would say:
> _"Out of sight, out of mind"_

In reality, that will result this:
```java
public class OrderController {
  private final OrderService orderService;

  @Value("${polling-interval-in-seconds}")
  private int pollingIntervalInSeconds;

  @Value("${timeout-in-seconds}")
  private int timeoutInSeconds;

  public OrderConstructor(OrderService orderService) {
    this.orderService = orderService;
  }
}
```

In case, _still_ you don't see the point... let's dive in.

We're having two points of injection here:
1. At the constructor:
    ```java
    public OrderConstructor(OrderService orderService) {
      this.orderService = orderService;
    }
    ```

2. By _field_ reflection:
    ```java
    @Value("${polling-interval-in-seconds}")
    private int pollingIntervalInSeconds;

    @Value("${timeout-in-seconds}")
    private int timeoutInSeconds;
    ```

The best approach would be:

1. Using `lombok` and using `final` keyword _(did you catch that?)_.
```java
    @RequiredArgsConstructor
    public class OrderController {
      private final OrderService orderService;

      @Value("${polling-interval-in-seconds}")
      private final int pollingIntervalInSeconds;

      @Value("${timeout-in-seconds}")
      private final int timeoutInSeconds;
    }
```

2. Without lombok:
```java
    public class OrderController {
      private final OrderService orderService;
      private final int pollingIntervalInSeconds;
      private final int timeoutInSeconds

      public OrderConstructor(
        OrderService orderService
        @Value("${polling-interval-in-seconds}") int pollingIntervalInSeconds,
        @Value("${timeout-in-seconds}") int timeoutInSeconds) {    
        this.orderService = orderService;
        this.pollingIntervalInSeconds = pollingIntervalInSeconds;
        this.timeoutInSeconds = timeoutInSeconds;
      }
    }
```
At least you've a good reason to split the instantiation, always centralize it in one way.

It'll give you a clear, objective and fast feedback like:
1. Where you're injecting the deps.
2. Which deps your class depends on.
3. 🧪 You'll be happy when testing! (do you test, right?)

Isn't so difficult to right clean, simple and objective code.
What make you a better developer is keep eye on the details.
Don't use reflection without an explicity reason.
It's expensive, it brake encapsulation principle in OOP.
Don't just ctrl + c and ctrl + v.
Don't get blinded from lombok hidden stuffs from you.


_xoff_.