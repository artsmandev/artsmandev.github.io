---
title: Create constants in a fashion way
description: Constants without going to interface or utility class(private constructor and so on)
tags: [Java, Enum]
---

Hey yo,
I'm not a big fan of constants.
It's true in all project I've worked... they always comes in.
But it isn't because I don't like, I can't bring new approaches to it, right?

Let's say we've these fields to be shared as _constants_:
```java
  String provider = "SomeProvider";
  String schemaVersion = "0.0.1";
  String productVersion = "V3";
```

The more common ways to make it as constants are:

1. Creating an **Interface**, e.g.:
```java
interface Constant {
  String PROVIDER = "SomeProvider";
  String SCHEMA_VERSION = "0.0.1";
  String PRODUCT_VERSION = "V3";
}
```
_Personally I don't like it 'cause interfaces have another purpose to me._

2. Creating a well known **Utility Class**:
```java
final class Constant {
	private Constant() {}
	public static final String PROVIDER = "SomeProvider";
	public static final String SCHEMA_VERSION = "0.0.1";
	public static final String PRODUCT_VERSION = "V3";
}
```
_A final class, private constructor, all public static... i don't like it either._

3. Creating **Enum**:
```java
enum Constant {
  PROVIDER("SomeProvider"), 
  SCHEMA_VERSION("0.0.1"), 
  PRODUCT_VERSION("V3");

	private final String value;

	Constant(String value) {
		this.value = value;
	}

	String value() {
		return value;
	}
}
```
_I like this one, but, create a new field, constructor, expose to a new method... nah, thanks._

I'll bring my personal _trick_ approach:
```java
enum Constant {;
  static final String PROVIDER = "SomeProvider";
  static final String SCHEMA_VERSION = "0.0.1";
  static final String PRODUCT_VERSION = "V3";
}
```

No _Interface_.
No _Utility Class_(final class, private constructor, static things).

Just _Enum_.
Simple, concise and objective.

Another post, another real life scenario.
See ya!

_xoff_.