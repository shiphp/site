---
layout: post
title: "Using Docker to Run a PHP and MySQL Application"
author: "Karl Hughes"
categories: [tutorials]
tags: [php,docker,intermediate,mysql]
---

![](https://i.imgur.com/2dFw58U.jpg)

One of the most common operations for any application is to connect to a
database, but installing multiple databases locally can be a tricky process. You
have to make sure everyone on your team has the right version and that they’re
connecting the same way. Fortunately, Docker makes this really simple and more
transferrable.

### 1. Write a PHP Script that Connects to a MySQL Database

Let’s start by writing a simple PHP script that will connect to a MySQL
database. All the script is going to do is connect and let us know that the
connection was successful. This isn’t very exciting, but it’s a starting point
for any complex PHP and MySQL app.

#### index.php

    <?php $mysqli = new mysqli("database", "admin", "12dlql*41");
    echo $mysqli->server_info . 
    ;

This file connects to a MySQL database using `mysqli` and then shows the
database’s version number in the terminal. Good enough to test things out.

### 2. Start a MySQL Database Container

Next, we need to start up a MySQL container. When using Docker, you want to
think about each service or application as its own unique entity, so we need to
start each container separately. Here’s the Docker command to run a MySQL
container:

    docker run -d --name database -e 
    =admin -e 
    =12dlql*41 -e MYSQL_RANDOM_ROOT_PASSWORD=true mysql:5

#### What’s going on here?

* `docker run` is Docker’s command to [run a command within a new
container](https://docs.docker.com/engine/reference/run/).
* `-d` tells the container to start in “detached” mode so that we can continue to
work in the terminal while the container is running.
* `--name database` is a name we’ll use to define this container when we reference
it within our PHP container later.
* `-e MYSQL_USER=admin` defines the MySQL user for logging into the server.
* `-e MYSQL_PASSWORD=12dlql*41` defines the MySQL password.
* `-e MYSQL_RANDOM_ROOT_PASSWORD=true` tells the container to set a strong, random
`root` user password. We don’t want to use the root user for this example, so
setting it to something random helps keep our database more secure.
* `mysql:5` specifies the image to use. More MySQL images are available on [Docker
Hub](https://hub.docker.com/_/mysql/).

### 3. Run the PHP Script Within a Container

In a previous demo, we covered [running a PHP script in a Docker
container](https://medium.com/shiphp/running-a-php-script-in-a-docker-container-d9e6142bae11).
You’ll notice that this Docker run command is different, but after you run it,
you should see the MySQL version in your command line (something like `5.7.18`).

    docker run --rm -v $(pwd):/app -w /app --link database tommylau/php php index.php

#### What does this script do?

* `docker run --rm -v $(pwd):/app -w /app` tells docker to create a removable
container with a mounted volume containing our code in its working directory.
There are [more details in my post on running a PHP script in a Docker
container](https://medium.com/shiphp/running-a-php-script-in-a-docker-container-d9e6142bae11).
* `--link database` this links the database container we just created with the PHP
container we’re making now. By default Docker won’t let containers talk to one
another (which is great for security), but you can specify links using the
`--link` option.
* `tommylau/php` instead of using the standard PHP images, I opted to use [another
user’s image](https://hub.docker.com/r/tommylau/php/) because it includes
support for the PHP MySQLi extension. You could also create your won Dockerfile
with your own custom extensions, but that’s beyond the scope of this tutorial.
* `php index.php` finally this command runs the PHP file that we created above.

While Docker may seem like a new way of thinking about your applications, once
you learn it, I’ve found that it can be a great asset for writing and testing
PHP code. Using modifications of the above example you could connect to a
Postgres database, MariaDB, or pretty much anything else.
