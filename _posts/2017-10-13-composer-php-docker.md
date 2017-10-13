---
layout: post
title: "Installing PHP Packages with Docker and Composer"
author: "Karl Hughes"
categories: [tutorials]
tags: [php,docker,intermediate]
---

![](https://i.imgur.com/asQ0O0C.jpg)

Package management is a method for importing code (often from open source
libraries) and keeping dependencies up to date in a software development
project. In PHP, we have a couple ways to manage packages, but the dominant
choice for modern PHP development is [Composer](https://getcomposer.org/).

While you can install PHP and Composer on your local machine directly, in this
tutorial we’ll use Docker, which allows us to run PHP and Composer scripts
without configuring either on our system directly. If you haven’t already, you
should [install Docker for your operating
system](https://docs.docker.com/engine/installation/).

### Creating a PHP Timer Script

We are going to write a PHP script that sleeps for between 1 and 3 seconds, then
tells us how long it waited. It’s very simple using the [PHP Timer
package](https://github.com/sebastianbergmann/php-timer), but we’ll need to
import it.

#### composer.json

Create a new file called `composer.json` and add the following to it:

    {
        "require": {
            "phpunit/php-timer": "^1.0.9"
        }
    }

This tells Composer about our dependencies — in this case, just one package —
when we run the install command.

#### Installing the Dependencies

Next, we will install the dependencies using [the official Composer Docker
image](https://hub.docker.com/r/composer/composer/):

    docker run --rm -v $(pwd):/app composer/composer:latest install

Let’s look at this docker command and see what’s going on:

* `docker run` is Docker’s command to [run a command within a new
container](https://docs.docker.com/engine/reference/run/). There are a lot of
options that you can pass in, so be sure to read the documentation.
* `--rm` tells Docker to “remove” the container after the command is run.
Alternatively, you can save the container and name it to run it again.
* `-v $(pwd):/app` is telling Docker to [mount a
volume](https://docs.docker.com/engine/tutorials/dockervolumes/). You typically
pass in a path to a folder on your root directory, a colon, and then a path to
the folder in the container. Here we are mounting the current directory (along
with the `composer.json` file we created) into a container. This mounting goes
both ways, so we will also see the `/vendor` folder created when Composer runs.
* `composer/composer:latest` is the actual image we’re using for this container.
This will install the packages for PHP 7, but Composer also has images for older
versions of PHP.
* `install` is the Composer command that is going to be run. You can also do
`update` or `dump-autoload` in this same manner.

Now all the dependencies are installed in a new `/vendor` folder, but we still
have to import them and write our PHP script.

#### The PHP Script

Our PHP script imports the dependencies that are included in Composer’s
`vendor/autoload.php` file, starts a timer, waits between 1 and 3 seconds, then
outputs the final time that the script took to run. This part of the tutorial
works the same way whether you use Docker or not. This file should be named
`index.php`:

    <?php require('vendor/autoload.php');
    \PHP_Timer::start();
    sleep(rand(1, 3));
    $time = \PHP_Timer::stop();
    print \PHP_Timer::secondsToTimeString($time);

#### Running the PHP Script

Finally, we just need to run the new PHP script we created in another Docker
container. This is covered in more detail in our tutorial on running PHP scripts
in Docker, but the final command should be:

    docker run --rm -v $(pwd):/app -w /app php:cli php index.php

You should see either `1 second`, `2 seconds`, or `3 seconds` on your terminal
output.
