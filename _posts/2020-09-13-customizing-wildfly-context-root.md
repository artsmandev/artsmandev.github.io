---
title: Customizing Wildfly's context root
description: In this post we will learn how to personalize Wildfly's context root
categories:
 - tutorial
tags: WildFly
---

Developing for Java Web default context path is generated from artifact's name without your extension.

For example, if `artsmanBlog.war` is an artifact, the context path will be `artsmanBlog`:<br/>
>http://localhost:8080/`artsmanBlog`

Working with Wildfly 20.0.1.Final, change the context path is easy:<br/>

1. Create a xml file named `jboss-web.xml`
2. Put it inside of the WEB-INF<br/>
3. Define the new context path inside `context-root` tag<br/>

Like below:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<jboss-web xmlns="http://www.jboss.com/xml/ns/javaee"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://www.jboss.com/xml/ns/javaee
                               http://www.jboss.org/schema/jbossas/jboss-web_14_0.xsd"
           version="14.0">
  <context-root>/myCustomContextRoot</context-root>
</jboss-web>
```

Now, the context path will be `myCustomContextRoot`:<br/>
>http://localhost:8080/`myCustomContextRoot`

*xoff*.