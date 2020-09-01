---
title: First Post
description: The beginning...
categories:
tags:
---

> Yep!

```java
package dev.artsman.blog.post;

class First {

  public static void main(String[] args) {
    new Post("Hello World, 🤪.").publish();
  }

}

class Post {
  String content;

  Post(String content) {
    this.content = content;
  }

  void publish() {
    System.out.println(this.content);
  }

}
```
*eof.*
