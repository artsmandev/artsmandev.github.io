---
title: Optional as input methods
description: Yes you can, you're Kotlin friend... don't do it in Java, in reality it hides bad design
tags: [Optional]
---

The Optional wasn't designed to be used as a _input_.
The Optional was designed to the an _output_.
It's like a container that may or may not have a non-null value in returns.
An alternative to return `null`.
To avoid `obj == null`, more objective, avoiding `NPE`.

Even though you can use it as an input method.
Because you like Kotlin and want to use Java as Kotlin... don't do that.

People argue that:
> we'll use Optional as input to avoid NPE.

Dude, really?
We've many other ways to validate an input method in Java.
Did you heard about "_Jakarta Validation_"?

Using `Optional` as input can lead to designs like this (simplified):
```java
Token parseToken(Optional<String> optionalToken) {
  if (optionalToken.isEmpty()) {
    return null;
  }
  try {
    return parser.parse(optionalToken.get(), Token.class);
  } catch (Exception e) {
    return null;
  }
}
```

That's clearly a bad design!
Why do you have an Optional input?
It'll be more confusing if you try(you do it, right?) to create a test:
```java
sut.parse(Optional.empty()); // when input is empty?
sut.parse(Optional.ofNullable(null)) // when input is null?
sut.parse(Optional.ofNullable("    ")) // when input is invalid?
sut.parse(Optinal.of("valid-token-input")) // when input is valid?
```
Pay attention about how ugly they are.
{: .post-caption}

1st make a boundary in your method, only allow get into the method valid state.
Just by `@NotBlank` you you not allow _null_ or _blank_:
```java
Token parseToken(@NotBlank String tokenInput) {...}
```

2nd use `Optional` as it should be used, as _output_:
```java
Optional<Token> parseToken(@NotBlank String tokenInput) {
  try {
    var token = parser.parse(tokenInput.get(), Token.class);
    return Optional.of(token);
  } catch (Exception e) {
    return Optional.empty();
  }
}
```

The code is:
1. a way clean.
2. has an effective boundary about what `state` is expected to get inside method.
3. readable.
4. objective.

Use the API as it's intended to be.
Use it correctly, be smart, don't force "similar" behavior where it doesn't apply.
Java is beautiful, so create clean and more maintainable code.

_xoff_.