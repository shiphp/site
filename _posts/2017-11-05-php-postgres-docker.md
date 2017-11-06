---
layout: post
title: "Running a PHP Application with Postgres and Docker"
author: "Karl Hughes"
categories: [tutorials]
tags: [php,docker,intermediate,postgres]
---

![](https://i.imgur.com/4YGjEZr.jpg)

[MySQL may be the standard
choice](https://medium.com/shiphp/using-docker-to-run-a-php-and-mysql-application-b89f89098cc5)
in relational databases for PHP developers, but Postgres is a great option as
well. The setup is very similar, and running Postgres and PHP within Docker
containers is just as easy.

### 1. Create a PHP Script that Connects to Postgres

Let’s start by writing a simple PHP script that will connect to a Postgresql
database. All the script is going to do is connect and let us know that the
connection was successful:

#### index.php

    <?php

    // Connect to the database
    $dbconn = pg_connect("host=database dbname=admin user=admin password=4LyM7F39w97n");
    // Show the client and server versions
    print_r(pg_version($dbconn));

This file connects to a Postgres database using `pg_connect` and then shows the
database’s version number in the terminal. This isn’t a very useful real-world
script, but it’s a great way to test our Docker setup.

### 2. Start the Postgres Container

Next, we want to start up a Postgres Container. In this case, we’re using
version 9.6 from [Postgres’ official Docker
image](https://hub.docker.com/_/postgres/):

    docker run -d --rm --name database -e POSTGRES_USER=admin -e POSTGRES_PASSWORD=4LyM7F39w97n postgres:9.6

#### What’s going on here?

* `docker run` is Docker’s command to [run a command within a new
container](https://docs.docker.com/engine/reference/run/).
* `-d` tells the container to start in “detached” mode so that we can continue to
work in the terminal while the container is running.
* `--rm` will “clean up” the container by removing it after we shut it down.
* `--name database` is a name we’ll use to define this container when we reference
it within our PHP container later.
* `-e POSTGRES_USER=admin` defines the Postgres user for logging into the server.
* `-e POSTGRES_PASSWORD=4LyM7F39w97n` defines the Postgres database password.
* `postgres:9.6` specifies the image to use. It’s usually a good idea to specify
an exact version number for databases to ensure nothing breaks when you migrate
your data.

### 3. Run the PHP Script within a Container

When we run the PHP script we want to make sure it connects to the Postgres
container and that it has all the required Postgresql extensions. Since I’ve
been using Postgres quite a bit lately, [I’ve created a Docker image for just
such an occasion](https://hub.docker.com/r/karllhughes/php-cli-postgres/), but
feel free to create your own if you’d prefer.

The command I ended up with is:

    docker run --rm -v $(pwd):/app -w /app --link database karllhughes/php-cli-postgres php index.php

#### What’s going on here?

* `docker run --rm -v $(pwd):/app -w /app` tells docker to create a removable
container with a mounted volume containing our code in its working directory.
There are [more details in my post on running a PHP script in a Docker
container](https://medium.com/shiphp/running-a-php-script-in-a-docker-container-d9e6142bae11).
* `--link database` this links the database container we just created with the PHP
container we’re making now. By default Docker won’t let containers talk to one
another (which is great for security), but you can specify links using the
`--link` option.
* `karllhughes/php-cli-postgres` instead of using the standard PHP images, I
created a PHP CLI image with Postgres extensions installed. You could also
create your won Dockerfile with your own custom extensions, but that’s beyond
the scope of this tutorial.
* `php index.php` finally this command runs the PHP file that we created above.

If everything worked properly, you should see an array with all the database
version info:

    Array
    (
        [client] => 9.4.12
        [protocol] => 3
        [server] => 9.6.3
        [server_encoding] => UTF8
        [client_encoding] => UTF8
        [is_superuser] => on
        [session_authorization] => admin
        [DateStyle] => ISO, MDY
        [IntervalStyle] => postgres
        [TimeZone] => UTC
        [integer_datetimes] => on
        [standard_conforming_strings] => on
        [application_name] => 
    )

You’ve now connected a PHP command line script with a Postgres database
container. There’s a lot of work left if you want to make this a useful
application, but the rest is just code.
