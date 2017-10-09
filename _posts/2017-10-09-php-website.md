---
layout: post
title: "Making Your First Website with PHP"
author: "Karl Hughes"
categories: [tutorials]
tags: [php,beginner]
---

![](https://i.imgur.com/XjFHZVt.jpg)

In order to build a working website on your computer that is powered by PHP,
you’ll need to first have [PHP installed](http://php.net/manual/en/install.php)
and know some basic HTML. In this tutorial we will make a website that displays
variables written in PHP code to users in an HTML document. Let’s get started!

1.  Open a text editor and create a file called `index.html`
1.  Add the following code to your new file:

```
<!doctype html>

<html lang="en">
<head>
  <meta charset="utf-8">
  <title>My PHP Website</title>
</head>

<body>
  <h1>My PHP Website</h1>
  <p>Here is some static content.</p>
</body>
</html>
```

This is [HTML code](https://www.w3schools.com/html/), which can be seen in a web
browser. You can open this file in Google Chrome or Firefox and you should see a
website like this:

![](https://i.imgur.com/vFUCCeN.png)

It’s not very pretty, but it’s a simple website!

Right now this website is static, meaning that there’s not a server or any PHP
code powering it. Next, we’ll convert this simple static web page into a dynamic
one with PHP:

1.  Rename your `index.html` file to `index.php`
1.  Open a command line terminal and navigate to the `index.php` file.
1.  PHP comes with [a built-in web
server](http://php.net/manual/en/features.commandline.webserver.php). We’ll use
this to try out our new webpage by running `php -S localhost:8000`
1.  Now open `http://localhost:8000` in your web browser. You should see the same
thing we saw before with the static HTML page.
1.  Open the `index.php` file in a text editor. Below the first `<p>` tags, create a
new set of `<p>` tags and add the following PHP code:

    <p><?php echo "Here is some dynamic content"; ?></p>

Now when you refresh `http://localhost:8000` you will see a new line. This line
was generated in PHP, and now our site is truly dynamic!

![](https://i.imgur.com/YGGoDnW.png)

### References

#### 1. index.html

    <!doctype html>

    <html lang="en">
    <head>
      <meta charset="utf-8">
      <title>My PHP Website</title>
    </head>

    <body>
      <h1>My PHP Website</h1>
      <p>Here is some static content.</p>
    </body>
    </html>

#### 2. index.php

    <!doctype html>

    <html lang="en">
    <head>
      <meta charset="utf-8">
      <title>My PHP Website</title>
    </head>

    <body>
      <h1>My PHP Website</h1>
      <p>Here is some static content.</p>
      <p><?php echo "Here is some dynamic content"; ?></p>
    </body>
    </html>
