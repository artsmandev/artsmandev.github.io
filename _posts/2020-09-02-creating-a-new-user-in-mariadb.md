---
title: Creating a new user in MariaDB
description: In this post we will learn how to create a new user in MariaDB
categories:
 - tutorial
tags: MariaDB
---

Hi there.

This week I got a problem with MariaDB.<br />
I don't know the reason, but, MariaDB's default credentials stopped work.<br />
After some tests, I only fix that creating a new user in MariaDB.

The process is a piece of cake, look at below:

After log in in MariaDB with `root`:

```sql
CREATE USER 'newUser'@'localhost' IDENTIFIED BY 'newUserPassword';
```

```sql
GRANT ALL PRIVILEGES ON *.* TO 'newUser'@'localhost' WITH GRANT OPTION;
```
*eof.*
