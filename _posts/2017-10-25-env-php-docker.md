---
layout: post
title: "Environmental Variables in PHP and Docker"
author: "Karl Hughes"
categories: [tutorials]
tags: [php,docker,intermediate]
---

![](https://i.imgur.com/hvB161p.jpg)

# Environmental Variables in PHP and Docker

When running a PHP application, you almost always want to keep some
environmentally dependent variables stored outside of your codebase. For
example, it’s not a good idea to hard-code your database connection information
because then you will have to remember to switch between local and production
details while running your app. That’s a sure-fire way to screw something up.

There are a few ways to handle environmental variables in PHP when running
Docker containers, so in this post we’ll look at four options. For reference,
our PHP file (let’s call it `index.php`) is going to be very simple, and just
output the value of a environmental variable called `ENV_VAR`:

    <?php echo getenv('ENV_VAR');

### 1. Docker “-e” flag

Docker provides the `-e` flag for passing environmental variables into any
container via the `docker run` command. For example, we could run the PHP script
above with the following:

    docker run --rm -v $(pwd):/app -w /app -e ENV_VAR='Hello World!' php:cli php index.php

Most of the options in this `docker run` command are covered in [the tutorial on
running PHP scripts within
containers](https://www.shiphp.com/blog/2017/php-script-in-docker),
but the last flag `-e ENV_VAR='Hello World!'` sets a container-wide variable
that the PHP script can access, and you should see `Hello World!` in the command
line when running this.

### 2. Docker “ — env-file” flag

If you have a lot of environmental variables, it may make sense to put them into
an environmental variables file. Docker can load all the variables from the file
by passing in the filename like this:

    docker run --rm -v $(pwd):/app -w /app --env-file .env php:cli php index.php

Your `.env` file should have at least this line:

    ENV_VAR=Hello World!

### 3. Dockerfile ENV Variables

Another way to include environmental variables is to include them in your
Dockerfile. This may not be good for variables you want to keep secret as
they’re going to be accessible by anyone with the built image you distribute,
but sometimes they make it easier when you want to use some values for
production and others for dev environments. First, we need to create a
`Dockerfile` for this project:

    FROM php:cli
    ENV ENV_VAR='Hello World!'

And now we need to build this image:

    docker build . -t envtest

Finally we can run the container using the new image we just created:

    docker run --rm -v $(pwd):/app -w /app envtest php index.php

### 4. Mounting a Volume and Using the Dotenv Composer Package

The last option we’ll cover works well if you’re using a hybrid Docker approach.
If some of your devs or servers are using Docker and others are not, you can
mount your `.env` file in a volume and load it with the [PHP Dotenv
package](https://github.com/vlucas/phpdotenv). You’ll need to install the
package first, and modify your PHP script a little.

First, [install the phpdotenv
package](https://github.com/vlucas/phpdotenv#installation-with-composer) (for
more info on installing Composer Packages with Docker, [see this
tutorial](https://www.shiphp.com/blog/2017/composer-php-docker)):

    docker run --rm -v $(pwd):/app composer/composer:latest require vlucas/phpdotenv

Next, update your PHP script to load the package:

    <?php require('vendor/autoload.php');
    $dotenv = new Dotenv\Dotenv(
    );
    $dotenv->load();
    echo getenv('ENV_VAR');

You’ll also need to make sure your `.env` file uses quotes around any values
with spaces:

    ENV_VAR="Hello World!"

And then you can run the PHP script [using your Docker command as we did in the
previous
tutorial](https://www.shiphp.com/blog/2017/php-script-in-docker):

    docker run --rm -v $(pwd):/app -w /app php:cli php index.php

Now you have multiple options for loading environmental variables in PHP using
Docker containers.
