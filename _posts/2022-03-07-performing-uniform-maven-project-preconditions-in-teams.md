---
title: Performing uniform Maven project preconditions in teams
description: Using Maven Enforce Plugin to add constraints and control Maven projects configs
tags: [Maven Enforcer Plugin]
---

Guys,

In this post, I will share a bit about amazing things we can do using Maven.<br/>
There are good plugins in Maven that we rarely hear about it. Sad.<br/>
`Maven Enforcer Plugin` is one of them.

Well,<br/>
Working with Maven we deal with many configs, plugins, dependencies, and your versions.<br/>
Doing the management of it, sometimes, can make your head hurt.

We can work alone, or, we can work in a team.<br/>
And people are incredibly stunning, each one with your own style.<br/>
When I say style, it's in a full context(IDE, numbers of space in tabs(2 of 4?, =D), versions of software, and so on).

Let's begin with the more simple one.

Everybody knows that Java is free, open-source, etc.<br/>
We can download it, create a program, make it works,  lunch that in Production.... huh... Nah!<br/>
But, maybe, you could forget that Oracle JDK it's not allowed to be put in Production without a license - yeah.<br/>
Now, imagine, a member of your team, using that one to compile, create an artifact, and push it to Nexus, running pipeline and `Voilà` it's there on Production, and we have a huge problem now.

>Note: I know there's OpenJDK version, but, that's not the point, OK?<br/>
>Thanks for understanding, kisses/hugs, and going on. (=


Let's imagine we are working on a team using only __AWS stack__.<br/>
At that moment, after talking with a team, we've decided to use __only__ __Amazon Vendor JDK Corretto__.<br/>
Only just for better compatibility and, you know, it has improvements performing to the cloud.

How can we ensure those two points:
1. Do not put Oracle JDK binaries without license in Production
2. Only use the vendor-specific to our architecture

Using `Maven Enforcer Plugin`!

`Step one` is declare the plugin and setup the version:
```xml
<build>
  <pluginManagement>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-enforcer-plugin</artifactId>
        <version>3.0.0</version>
      </plugin>
    </plugins>
  </pluginManagement>
</build>
```

`Step two` is setup the plugin:
```xml
<plugins>
  <plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-enforcer-plugin</artifactId>
    <execution>
      <id>enforce-vendor</id>
      <goals>
        <goal>enforce</goal>
      </goals>
      <configuration>
        <rules>
          <requireJavaVendor>
            <includes>
              <include>Amazon.com Inc.</include>
            </includes>
          </requireJavaVendor>
        </rules>
      </configuration>
    </execution>
  </plugin>
</plugins>
```

In this example I'm using the wrong Oracle's JDK:
```shell
❯ java -version                                            
java version "17.0.2" 2022-01-18 LTS
Java(TM) SE Runtime Environment (build 17.0.2+8-LTS-86)
Java HotSpot(TM) 64-Bit Server VM (build 17.0.2+8-LTS-86, mixed mode, sharing)
```

But, __OK__, we can confirm that JVM is from Oracle Corporation that easy way:

```shell
❯ jshell
|  Welcome to JShell -- Version 17.0.2
|  For an introduction type: /help intro

jshell> System.out.println(System.getProperty("java.vendor"));
Oracle Corporation

jshell> /exit
|  Goodbye
```

Oh, you notice? JShell said bye to me. Oh, my lovely Java. So polite. <3

When I try to compile, or, use any other Maven phase, it will fail like that:

```shell
❯ mvn compile             
[INFO] Scanning for projects...
[INFO] 
[INFO] -----------------< dev.artsman:maven-enforcer-plugin >------------------
[INFO] Building maven-enforcer-plugin 1.0-SNAPSHOT
[INFO] --------------------------------[ jar ]---------------------------------
[INFO] 
[INFO] --- maven-enforcer-plugin:3.0.0:enforce (enforce-maven) @ maven-enforcer-plugin ---
[INFO] 
[INFO] --- maven-enforcer-plugin:3.0.0:enforce (enforce-vendor) @ maven-enforcer-plugin ---
[WARNING] Rule 0: org.apache.maven.plugins.enforcer.RequireJavaVendor failed with message:
Oracle Corporation is not an included Required Java Vendor
[INFO] ------------------------------------------------------------------------
[INFO] BUILD FAILURE
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  0.523 s
[INFO] Finished at: 2022-03-06T18:48:18-03:00
[INFO] ------------------------------------------------------------------------
```

The interesting in here is, that constraint verification runs at the first goal in Maven Lifecycle: validation.<br/>
That's super cool, isn't it?

__Bonus__... we can do more!

Imagine there's already a Maven version __3.9__ and __you want force that specific one__.<br/>
Mine's completely wrong at this momento, right?

```shell
❯ mvn --version
Apache Maven 3.8.4 (9b656c72d54e5bacbed989b64718c159fe39b537)
```

Running Maven:
```shell
❯ mvn compile  
[INFO] Scanning for projects...
[INFO] 
[INFO] -----------------< dev.artsman:maven-enforcer-plugin >------------------
[INFO] Building maven-enforcer-plugin 1.0-SNAPSHOT
[INFO] --------------------------------[ jar ]---------------------------------
[INFO] 
[INFO] --- maven-enforcer-plugin:3.0.0:enforce (enforce-maven) @ maven-enforcer-plugin ---
[WARNING] Rule 0: org.apache.maven.plugins.enforcer.RequireMavenVersion failed with message:
Detected Maven Version: 3.8.4 is not in the allowed range 3.9.
[INFO] ------------------------------------------------------------------------
[INFO] BUILD FAILURE
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  0.962 s
[INFO] Finished at: 2022-03-06T15:45:20-03:00
[INFO] ------------------------------------------------------------------------
```

__Also__ you can __specify the Java Version__ ignoring the vendor at this moment!
```shell
❯ java --version
openjdk 17.0.2 2022-01-18
```

But, for now, i want only be able to use Java 11.<br/>
Running Maven:
```shell
❯ mvn compile                            
[INFO] Scanning for projects...
[INFO] 
[INFO] -----------------< dev.artsman:maven-enforcer-plugin >------------------
[INFO] Building maven-enforcer-plugin 1.0-SNAPSHOT
[INFO] --------------------------------[ jar ]---------------------------------
[INFO] 
[INFO] --- maven-enforcer-plugin:3.0.0:enforce (enforce-maven) @ maven-enforcer-plugin ---
[INFO] 
[INFO] --- maven-enforcer-plugin:3.0.0:enforce (enforce-vendor) @ maven-enforcer-plugin ---
[INFO] 
[INFO] --- maven-enforcer-plugin:3.0.0:enforce (enforce-jdk) @ maven-enforcer-plugin ---
[WARNING] Rule 0: org.apache.maven.plugins.enforcer.RequireJavaVersion failed with message:
Detected JDK Version: 17.0.2 is not in the allowed range [11,11].
[INFO] ------------------------------------------------------------------------
[INFO] BUILD FAILURE
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  0.906 s
[INFO] Finished at: 2022-03-06T18:54:20-03:00
[INFO] ------------------------------------------------------------------------
```

There are more cooll stuffs.

But, in my opinion, that three constraints are the essentials:
1. Maven version
2. JDK vendor
3. JDK version

That's all for today.

The codebase is on my [GitHub](https://github.com/artsmandev/labs-maven/tree/maven-enforcer-plugin).

*xoff*.