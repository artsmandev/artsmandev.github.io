---
title: Records and Lombok as JDBC Entity
description: Records and Lombok are useful, when they are used wisely
tags: [Records, Lombok]
---

As a Java Developer we're today very familiar with `records`(right?).<br>
It isn't uncommon we've `Lombok` on project as well.<br>
These are good tools, when they are used wisely.<br>
At the moment we put them together as `JDBC Entities`, so, here strange things begin.

Let's say we want to represent a `Car`.<br>
Having `manufacturer`, `model`, `color` and `year`, just KISS.<br>
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

However, people obsessed by things like _avoid boilerplate_, _I love Lombok_, _let's make Java in a Functional way_, _immutability everywhere_, etc.<br>
The result, things like this beautiful code:
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

_Records_ + _Builder_ as a n`Entity`.<br>
You know, I don't disagree 100%, at all.<br>
The record can bring `immutability` on Spring JDBC stuffs, cool, fine.

My point is the mentality _boilerplate is evil, so, go use annotation to hide them..._<br>
Looks like a harmless code, until we _observe the details and see that_ below:

> **1st, we've an entity as record**

The records idea is fill all fields at creation.<br>
Using a Builder, they're like enemies.<br>
Record says: "You must pass all fields, mate".<br>
Builder says: "You're free to just fill what you want, be free my friend".

> **2nd, It's not visible to us by `@Builder` does**

It injects a _static class Builder_ inside the Entity.<br>
Builder brings a _lot static methods_ plus an extra `toBuilder` method.

> **3rd, It makes us break the contract**

On the example we have **all fields required**.<br>
But, using `builder()` we start to break the contract by defining only some the fields, e.g:

```java
CarEntity.builder()
  .manufacturer("Ferrari")
  .color("Red")
  .build();
```

What about the other ones required fields `model` and `year`?<br>
Or even this one more bizarre:

```java
CarEntity.builder().build();
```
Really? **Class** + **Static Class** + **builder()** + **build()**?<br>
It doesn't make any sense to our **Domain**.<br>
Even all fields were not required, I still see that `CarEntity.builder().build();`<br>
Where, in place, we should create a no args constructor in a fashion way: `new CarEntity()`.<br>
Period.

I'm not against records or Lombok.<br>
I'm against bad pattern we bring to codebase when put all these things together.
We need to use them _wisely_.<br>

Thing about the Domain we're creating, understand?<br>
A good _class design prevent us vulnerabilities for bugs_.<br>
Don't be blinded about no boilerplate, function programming, immutability, records everywhere.<br>
Use the language's APIs as it's designed to be used.

_xoff_.