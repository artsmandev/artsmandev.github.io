---
title: Records and Lombok as JDBC Entity
description: Records and Lombok are useful, when they are used wisely
tags: [Records, Lombok]
---

As a Java Developer we're today very familiar with `records`(right?).
It isn't uncommon we've `Lombok` on project as well.
These are good tools, when they are used wisely.
At the moment we put them together as `JDBC Entities`, so, here strange things begin.

Let's say we want to represent a `Car`.
Having `manufacturer`, `model`, `color` and `year`, just KISS.
A scenario where you're using `Spring Data JDBC`.

The most common is doing something like:
```java
@Table(name = "car")
class CarEntity {
  @NotNull
  String manufacturer;
  @NotNull
  String model;
  @NotNull
  String color;
  @NotNull
  Year year;
}
```

However, people obsessed by things like _avoid boilerplate_, _I love Lombok_, _let's make Java in a Functional way_, _immutability everywhere_, etc. The result, things like this beautiful code:
```java
@Builder(toBuilder = true)
@Table(name = "car")
record CarEntity(
  @NotNull
  String manufacturer,
  @NotNull
  String model,
  @NotNull
  String color,
  @NotNull
  Year year
){}
```

_Records_ + _Builder_ as an `Entity`.
You know, I don't disagree 100%, at all.
The record can bring `immutability` on Spring JDBC stuffs, cool, fine.

My point is the mentality _boilerplate is evil, so, go use annotation to hide them..._
Looks like a harmless code, until we _observe the details and see that_ below.

> **1st, we've an entity as record**
{: .quote-separated}
The records idea is fill all fields at creation.
Using a Builder, they're like enemies.
Record says: "You must pass all fields, mate".
Builder says: "You're free to just fill what you want, be free my friend".

> **2nd, It's not visible to us by `@Builder` does**
{: .quote-separated}

It injects a _static class Builder_ inside the Entity.
Builder brings a _lot static methods_ plus an extra `toBuilder` method.

> **3rd, It makes us break the contract**
{: .quote-separated}

On the example we have **all fields required**.
But, using `builder()` we start to break the contract by defining only some the fields, e.g:

```java
CarEntity.builder()
  .manufacturer("Ferrari")
  .color("Red")
  .build();
```

What about the other ones required fields `model` and `year`?
Or even this one more bizarre:

```java
CarEntity.builder().build();
```
Really? **Class** + **Static Class** + **builder()** + **build()**?
It doesn't make any sense to our **Domain**.
Even all fields were not required, I still see that `CarEntity.builder().build();`
Where, in place, we should create a no args constructor in a fashion way: `new CarEntity()`.
Period.

I'm not against records or Lombok.
I'm against bad pattern we bring to codebase when put all these things together.
We need to use them _wisely_.

Think about the Domain we're creating, understand?
A good _class design prevent us vulnerabilities for bugs_.
Don't be blinded about no boilerplate, function programming, immutability, records everywhere.
Use the language's APIs as it's designed to be used.

_xoff_.