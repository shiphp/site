---
layout: post
title: "Running a PHP Script in a Docker Container"
author: "Karl Hughes"
categories: [tutorials]
tags: [tutorials,php,docker,intermediate]
---

![](https://i.imgur.com/RI8zECF.jpg?1)

If you’re used to running [PHP command line
applications](https://www.shiphp.com/blog/2017/php-command-line-script),
adding Docker could be a great way to increase the reusability and portability
of your scripts. Docker allows you to run your code in isolated containers
rather than on the host machine directly, meaning that even if you don’t have
the same version of PHP or extensions installed, you can be sure that your
script will run the same way on anyone’s Docker container anyway.

*Before we start, you should be familiar with [writing command line
scripts](https://www.shiphp.com/blog/2017/php-command-line-script)
in PHP and you should have [Docker
installed](https://docs.docker.com/engine/installation/).

### Our PHP script

First, let’s look at a simple “Hello World” PHP script:

#### hello.php

    <?php

    $text = "Hello World!";

    echo $text;

Now, open up a terminal and navigate to the directory where you saved  . Let’s
run that script within a Docker container using the latest version of [PHP’s
official CLI container](https://hub.docker.com/_/php/):

    $ docker run --rm -v $(pwd):/app -w /app php:cli php hello.php

When run, you should see the PHP CLI container being downloaded (the first
time), then you should see the output `Hello World!`. You’ve just run a PHP script in Docker!

### But what does it mean?

Let’s go over that Docker command (`docker run...`) and see what is going on:

* `docker run` This is Docker’s command to [run a command within a new
container](https://docs.docker.com/engine/reference/run/). There are a lot of
options that you can pass in, but I tried to keep them pretty minimal for this
example.
* `--rm` This tells Docker to “remove” the container after the command is run.
Alternatively, you can save the container and name it to run it again.
* `-v $(pwd):/app` This is telling Docker to [mount a
volume](https://docs.docker.com/engine/tutorials/dockervolumes/). You typically
pass in a path to a folder on your root directory, a colon, and then a path to
the folder in the container. Volumes are a powerful tool, but for this simple
example we’re just mounting the [current
directory](https://www.computerhope.com/unix/upwd.htm) from our terminal into
the `/app` directory in the new Docker container.
* `-w /app` This defines the Docker container’s “working directory.” That’s the place where
it will look for code when you pass in a command to execute. Since we’re
mounting our code into  we obviously want Docker to run code from that
directory.
* `php:cli` The first three items in this list were Docker run flags, but `php:cli` is the actual
image we’re using for this container. There are other [PHP containers on Docker
Hub](https://hub.docker.com/_/php/), so be sure to check them out if you’re
doing something different like a web app or Apache server.
* `php hello.php` Finally, this is the actual command that we want to run on the container. It is
run relative to the working directory we defined above.

You may think that was a long command to write just to run a PHP script, but
think about what you didn’t have to do. You didn’t have to install PHP on your
machine, and you could run that command in different versions of PHP by tweaking
the `php:cli` parameter.

As you can see, it’s easy to run a PHP script within a Docker container. Docker
might not be right for every project and workflow, but if portability and
modularity are concerns, I’d highly recommend giving it a try.
