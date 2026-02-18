---
title: Enforcing JDK version in Gradle
description: Keep team using same version can be a challenge, but I bring an easy way to enforce that.
tags: [Gradle, Java, JDK, Build, Pipeline]
---

Last week a fried of mine faced a very special problem with a build in CI/CD pipeline.<br>
In its Dockerfile, he wasn't setting a specific JDK version.<br>
This reponsibility was so, automatically, transfered to the image `eclipse-temurin:21`.<br>
He was using version 21.0.**9** then image updated to 21.0.**10** and bang... build started to fail.

A some time I wanted to write about similar thing:
> How could we keep same JDK version when working in a team?

Is very easy to us, while working in a team, have different versions, e.g.:
1. In our local `JAVA_HOME`
2. In our IDE
3. In CI/CD
4. And so on

And indepently which JDK version manager you're using:
1. manually _(i was this guy a long time a go)_
2. sdkman _(today is my favorite)_
3. jenv
4. etc

It's very simple:
1. We create a file in root project that containing a version, e.g. `.java-version`:
    ```kotlin
    21.0.9
    ```

2. In Gradle(build.gradle.kts - yes I prefer kotlin over groove), set the toolchain to use that `major` version:
    ```kotlin
    val projectJdkFullVersion = file(".java-version").readText().trim()
    val projectJdkMajorVersion = projectJdkFullVersion.substringBefore('.').toInt()

    java {
      toolchain {
        languageVersion.set(JavaLanguageVersion.of(projectJdkMajorVersion))
      }
    }
    ```

3. We create a `task` that will check the version:
    ```kotlin
    val checkJavaVersion by tasks.registering(Task::class) {
      doLast {
        val jdkRuntimeVersion = System.getProperty("java.version")
        if (!jdkRuntimeVersion.startsWith(projectJdkFullVersion)) {
          throw GradleException("Requires JDK $projectJdkFullVersion, but found $jdkRuntimeVersion")
        }
      }
    }
```

4. Bind the task validation compile task in Gradle Lifecycle:
    ```kotlin
    tasks.compileJava {
      dependsOn(checkJavaVersion)
    }
    ```

In case the environment isn't using the expected version, it'll fail:
```shell
❯ gradlew build
> Task :checkJavaVersion FAILED

FAILURE: Build failed with an exception.

* What went wrong:
Execution failed for task ':checkJavaVersion'.
> Requires JDK 21.0.9, but found 21.0.10

* Try:
> Run with --stacktrace option to get the stack trace.
> Run with --info or --debug option to get more log output.
> Run with --scan to get full insights.
> Get more help at https://help.gradle.org.

BUILD FAILED in 974ms
1 actionable task: 1 executed
```
That's it.<br>
Everywhere your project is on, it'll always be running on same version and be consistent.

That care with your versions.<br>
When updating, even in case its a "low importance".<br>
Like the example, it was just "21.0.**9** to 21.**10**", it can result in fail on your end.<br>
Take a moment to read the _release notes_.<br>
Remember: if you don't assume the responsibility about your version, your code... someone will do it.<br>

_xoff_.