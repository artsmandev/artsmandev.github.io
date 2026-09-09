---
title: Using Unnamed Variables with CompletableFuture
description: Use Java’s unnamed lambda parameter (_) when CompletableFuture.allOf returns CompletableFuture<Void>
tags: [Java, CompletableFuture, Concurrency, Unnamed Variables, JDK 22]
---

Unnamed variables and patterns can be used when a declaration is required but the value is never used. They are denoted by the underscore character: `_`.
It was finalized in JDK 22 by JEP 456.
See [JEP 456](https://openjdk.org/jeps/456) for the full feature details.

It can be used in many ways.
However one useful place for it is concurrency code with `CompletableFuture`.
Mainly in scenarios with many asynchronous calls, e.g.:
```java
package dev.artsman.poc.concurrency;

import java.util.concurrent.CompletableFuture;
import java.util.function.Supplier;

class Main {
  public static void main(String[] args) {
    var priceFromFuture = CompletableFuture.supplyAsync(findPriceAsync());
    var shippingFromFuture = CompletableFuture.supplyAsync(findShippingAsync());
    var discountFromFuture = CompletableFuture.supplyAsync(findDiscountAsync());
  }

  private static Supplier<Integer> findPriceAsync() {
    return () -> 100;
  }

  private static Supplier<Integer> findShippingAsync() {
    return () -> 30;
  }

  private static Supplier<Integer> findDiscountAsync() {
    return () -> 20;
  }
}
```

When we need all async calls to complete before working with the data, `.allOf` comes in handy:
```java
//truncated
class Main {
  public static void main(String[] args) {
    //truncated
    CompletableFuture
      .allOf(priceFromFuture, shippingFromFuture, discountFromFuture);
  }
  //truncated
}
```

Then, with `.thenApply`, we can retrieve the values and perform the operation we need:
```java
//truncated
class Main {
  public static void main(String[] args) {
    //truncated
    CompletableFuture
      .allOf(priceFromFuture, shippingFromFuture, discountFromFuture)
      .thenApply(unused -> {
        int price = priceFromFuture.join();
        int shipping = shippingFromFuture.join();
        int discount = discountFromFuture.join();
        return price + shipping - discount;
      });
  }
  //truncated
}
```

Finally, we use `.thenAccept` to print the computation result:
```java
//truncated
class Main {
  public static void main(String[] args) {
    //truncated
    CompletableFuture
      .allOf(priceFromFuture, shippingFromFuture, discountFromFuture)
      .thenApply(unused -> {
        int price = priceFromFuture.join();
        int shipping = shippingFromFuture.join();
        int discount = discountFromFuture.join();
        return price + shipping - discount;
      })
      .thenAccept(System.out::println)
      .join();
  }
  //truncated
}
```
This prints the value: **110**.
{:.post-caption}

At this point, we can take advantage of _unnamed variables_:
```java
//truncated
class Main {
  public static void main(String[] args) {
    //truncated
      .thenApply(unused -> {
        int price = priceFromFuture.join();
        int shipping = shippingFromFuture.join();
        int discount = discountFromFuture.join();
        return price + shipping - discount;
      });
  }
  //truncated
}
```
The variable **unused** has no meaning here because its value is never used.
{:.post-caption}

Since `.allOf` returns a `CompletableFuture<Void>`.
The `.thenApply` parameter is always `null`.
Being just a required variable that is never used, we can replace it with `_`:
```java
//truncated
class Main {
  public static void main(String[] args) {
    //truncated
      .thenApply(_ -> {
        int price = priceFromFuture.join();
        int shipping = shippingFromFuture.join();
        int discount = discountFromFuture.join();
        return price + shipping - discount;
      });
  }
  //truncated
}
```

This is where _unnamed variables_ are beautiful: they remove a meaningless name and make the code cleaner.

_xoff_.
