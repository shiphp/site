---
layout: post
title: "Running a PHP Web App in a Docker Container"
author: "Karl Hughes"
tags: [tutorials,php,docker,intermediate]
---

![](https://i.imgur.com/BsVbhLr.jpg)

If you’re familiar with the process for creating a simple website using PHP,
then adding Docker to the mix should be relatively easy. The great thing about
using Docker is that you have better portability over your code. Let’s look at a
simple Docker command that will host a webpage written in PHP.

### Our PHP Script

#### index.php

Create a file called `index.php` and add some HTML like this:

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

As you can see, there’s one single line of PHP in the script just to make sure
our server is actually working.

Let’s serve that website using the official [PHP Apache
image](https://hub.docker.com/_/php/):

    docker run --rm -p 8000:80 -v $(pwd):/var/www/html php:apache

Docker should download the latest version of the image and start up a webpage at
[localhost:8000](http://localhost:8000/).

### What’s going on with that Docker command?

Let’s break down the `docker run` command piece by piece:

* `docker run` This is Docker’s command to [run a command within a new
container](https://docs.docker.com/engine/reference/run/). There are a lot of
options that you can pass in, but this example is pretty minimal.
* `--rm` This tells Docker to “remove” the container after the command is completed. In
this case, that means that when you exit (by pressing `control + c` on Mac), the container
will stop and remove itself from your system.
* `-p 8000:80` If you’re familiar with [Apache](https://httpd.apache.org/), you know that it
typically serves web applications on port 80. This parameter tells Docker to map
port 80 within the container to port 8000 on the host. That’s why our site is
available at .
* `-v $(pwd):/var/www/html` This is telling Docker to [mount a
volume](https://docs.docker.com/engine/tutorials/dockervolumes/). You typically
pass in a path to a folder on your root directory, a colon, and then a path to
the folder in the container. Volumes are a powerful tool, but for this simple
example we’re just mounting the [current
directory](https://www.computerhope.com/unix/upwd.htm) from our terminal into
the directory Apache serves by default.
* `php:apache` Here we specify the image to use for PHP. As I mentioned above, this image
includes the Apache webserver.

In just one line we were able to serve a PHP based website on our machine, and
if we pass that same Docker command to other developers, they will be able to do
the same thing without installing any new software. Unlike a traditional VM,
this command takes a few seconds to get up and running, and it’s very
lightweight.
