---
title: Getting variable from Java and using it on Javascript on JSP
description: In this post we will learn how to use Java's variable on Javascript on JSP
categories:
- tutorial
tags: JSP
---

Hello there,

I'm working with a project using `JSP` nowadays.
That project make uses of a lot of `Javascripts` calls.
`I don't understand` why people insists put on `JSP`: `scriptlets`, `CSS` and `Javascript` at the `same` page.
I, really, sure... `I don't uderstand`.

I going to show how we can use `Java's variable and use it on Javascript` method... at `another file`.

**The Controller:**
```java
package dev.artsman.blog;
/* ommited imports */
@WebServlet(name = "HomeController", urlPatterns = "/home")
public class HomeController extends HttpServlet {
  @Override
  protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    req.setAttribute("secretMessage", "Hello World!");
    req.getRequestDispatcher("/secret-message.jsp").forward(req, resp);
  }
}
```
Line `8`, We simplesly have set an atribute `secretMessage` on `request`.
{: .post-caption}
Line `9`, We `forward` that `request/response` to our view namming `secret-message.jsp`.
{: .post-caption}

**The JS:**
```javascript
/* WEB-INF/script.js */
function showSecretMessage() {
  document.getElementById('secret-message').innerHTML = secretMessage
}
```
This method only `insert` at element `secret-message` the `content` of one variable `secretMessage`.
{: .post-caption}

**The JSP:**
```java
<%@ page pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
  <head>
    <title>JSP Tips</title>
    <script type="text/javascript" src="script.js"></script>
  </head>
  <body>
    <h1>Getting message from Java and using on Javascript.</h1>
    <p>Click on button to see the secret message: <span id="secret-message"></span></p>
    <button type="button" onclick="showSecretMessage()">Show me!</button>
    <script type="text/javascript">
      let secretMessage = "${secretMessage}"
    </script>
  </body>
</html>
```
Line `6`, we simplesly make the import of the script.js.
{: .post-caption}
Line `10`, it's defined the place for show de message.
{: .post-caption}
Line `13`, at this point, we make the bind beetween Java's variable and Javascript!
{: .post-caption}

That allows us do what we need to do on right place:
* `Java's` code only in `JSP` file.
* `Javascript's` code in `JS` file.
  * We don't need use `var`, limit the scope using `let`!
![image text](/assets/images/posts/001.png "At this point, the binding it's already done.")
![image text](/assets/images/posts/002.png "Click on button and booom, it works!")

Why doing simple things appear so difficult?

*xoff*.