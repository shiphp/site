---
layout: post
title: "Running a SlimPHP Application in Docker Containers"
author: "Karl Hughes"
categories: [tutorials]
tags: [php,docker,intermediate,slimphp]
---

![](https://i.imgur.com/qJXcCqu.png)

Using Docker to run [a simple PHP web
application](https://www.shiphp.com/blog/2017/php-web-app-in-docker)
is one thing, but how hard is it to install and run a small framework like
[SlimPHP](https://www.slimframework.com/) in Docker containers? Let’s try it
out!

### 1. Installing SlimPHP with Docker and Composer

First we need to install SlimPHP into a new directory. Their [documented
installation instructions](https://www.slimframework.com/docs/start/installation.html) assume
that you’ve got Composer installed locally, but since we’re building with
Docker, we should use a container.

* First, make a new directory, navigate to it in your command line terminal.
* Next, install SlimPHP using the [Composer Docker
container](https://www.shiphp.com/blog/2017/composer-php-docker):

```
docker run --rm -v $(pwd):/app composer/composer:latest
```

Now you should see a `vendor/` directory as well as a `composer.json` and
`composer.lock` file in your project’s root. SlimPHP is now installed and we can
build a simple application.

### 2. Running a SlimPHP Application

SlimPHP is a microframework, meaning that they don’t include too much more than
a router, but since we just want to try it out, that’s good enough. [SlimPHP’s
documentation includes a simple
application](https://www.slimframework.com/docs/objects/application.html), so
let’s start with that.

* The application will live in an `index.php` file. Once you’ve created the file,
add the following PHP code to it:

```
<?php require 'vendor/autoload.php';
// instantiate the App object
$app = new \Slim\App();
// Add route callbacks
$app->get('/', function ($request, $response, $args) {
    return $response->withStatus(200)->write('Hello World!');
});
// Run application
$app->run();
```

* Now we can try running the small PHP application with the [official PHP/Apache
container](https://www.shiphp.com/blog/2017/php-web-app-in-docker):

```
docker run --rm -p 8000:80 -v $(pwd):/var/www/html php:apache
```

* Navigate to [http://localhost:8000/](http://localhost:8000/) and you should see
the text `Hello World!`

As you can see, without installing PHP, Composer, or anything besides Docker we
can get started building a PHP web application on SlimPHP. This makes
development fast and allows you to quickly share your code with others no matter
what environment they’re using.
