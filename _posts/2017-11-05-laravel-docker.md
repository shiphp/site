---
layout: post
title: "Running a Laravel Application in Docker"
author: "Karl Hughes"
categories: [tutorials]
tags: [php,docker,intermediate,laravel]
---

![](https://i.imgur.com/NSVvPKQ.png)

My favorite PHP framework for the past few years has been
[Laravel](https://laravel.com/). It combines modern best practices with
developer-friendly tools and pretty much all the common components that web
developers need.

There are already several good ways to set up a local development environment
for Laravel including [Homestead](https://laravel.com/docs/5.4/homestead) and
[Valet](https://laravel.com/docs/5.4/valet), but if you’re interested in using
Docker instead read on.

### Starting a Database Container

Most Laravel applications require a database for storage, and since PHP
developers tend to use MySQL, let’s start up a MySQL database container.

    docker run -d --name database -e 
    =admin -e 
    =4gdlylp21 -e MYSQL_RANDOM_ROOT_PASSWORD=true -e 
     mysql:5

This command is covered in more detail in a previous post on [using MySQL and
PHP with
Docker](https://medium.com/shiphp/using-docker-to-run-a-php-and-mysql-application-b89f89098cc5),
so check that out if you’re new at this.

### Installing Laravel with a Composer Container

Next we need to install Laravel, but since we’re trying to do things the Docker
way, we’ll use a Composer container and mounted volume to create the project.
Just run this command in your terminal to create a new Laravel project:

    docker run -v $(pwd):/app composer create-project --prefer-dist laravel/laravel laravel-docker

You should now see a folder called `laravel-docker` in your current directory.
Navigate to that folder and type `ls` to see all the Laravel files:

    app  composer.lock phpunit.xml routes  vendor

    artisan  config  public  server.php webpack.mix.js

    bootstrap database readme.md storage

    composer.json package.json resources tests

### Configuring Laravel

Because we’re going to use Apache as our webserver and we want to connect to the
database container we just created, there is some custom configuration to set
up. First, create a `.htaccess` file at the root of your new Laravel
installation:

    <IfModule mod_rewrite.c>
        RewriteEngine on
        RewriteCond %{REQUEST_URI} !^public
        RewriteRule ^(.*)$ public/$1 [L]
    </IfModule>

This will ensure that Laravel serves up the `/public` directory instead of the
root directory.

Next, update your `.env` file with the database credentials you set when
creating the database container:

    DB_CONNECTION=mysql
    DB_HOST=database
    DB_PORT=3306
    DB_DATABASE=laravel
    DB_USERNAME=admin
    DB_PASSWORD=4gdlylp21

### The Laravel Application Container

Next we want to run a PHP container. The easiest way to get a PHP project
started in Docker is to use one of the [official images on Docker
hub](https://hub.docker.com/_/php/), but because we need some additional
extensions installed, we’re going to use a Docker image [I created specifically
for running LAMP stack applications on
Docker](https://hub.docker.com/r/karllhughes/php-apache-mysql/):

    docker run --rm --name laravel-docker --link database -v $(pwd):/var/www/html -p 8080:80 karllhughes/php-apache-mysql

Once you’ve run the above command navigate your browser to `localhost:8080/`.
Laravel should be running and you can get started building your application in
Docker containers!

#### What’s going on here?

* `docker run` is the starting point for getting a container running. There are [a
lot of options you can pass in](https://docs.docker.com/engine/reference/run/),
but we’ll try to keep it pretty simple for this example.
* `--rm` tells Docker to remove the container after you shut it down. This ensures
you don’t have a bunch of unused containers hanging around.
* `--name laravel-docker` is optional, but nice if you run the container in
detached mode or if you run a lot of containers.
* `--link database` connects the Laravel container with your running MySQL
container.
* `-v $(pwd):/var/www/html` mounts the current code into a volume in the
container. This way you can make updates to the code and immediately see them
reflected in your application.
* `-p 8080:80` tells Docker to link port 8080 on your host machine with the
running container’s port 80. That’s where Apache is serving the website inside
the container.
* `karllhughes/php-apache-mysql` finally this is the name of the image we’re using
for this container. I put this image together for running PHP/Apache projects
with MySQL and all the required extensions.

There’s plenty more you can do with containers to improve your local Laravel
development process, but as you can see getting started is surprisingly easy.
With just a couple commands, we’ve got Laravel running and we didn’t even have
to install any additional software on our local machine.
