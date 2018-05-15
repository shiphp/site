---
layout: post
title: "Running SQLite in PHP with Docker"
author: "Karl Hughes"
categories: [tutorials]
tags: [php,docker,sqlite,databases]
---

[SQLite](https://www.sqlite.org/index.html) is a great database for getting started on small projects. Unlike traditional SQL databases (like [MySQL](https://www.shiphp.com/blog/2017/php-mysql-docker) or [Postgres](https://www.shiphp.com/blog/2017/php-postgres-docker)), SQLite stores all your records in a single flat file that you can easily edit, transfer, or even check into version control (if your project warrants it).

Another great feature of SQLite is that it's built into the [default PHP images on Docker Hub](https://hub.docker.com/_/php/), so you don't even have to start up another Docker container, and running a PHP application with a SQLite database is essentially a one-liner. Let's take a look at how you can incorporate SQLite into your Dockerized PHP apps.

![](https://i.imgur.com/U6fSWpz.jpg)

## The Code

First, let's take a look at a simple PHP script that connects to a SQLite database (called `testing.sqlite`), creates a table, and adds 3 users. Save this file as `index.php`:

```php
<?php

$db = new SQLite3('testing.sqlite', SQLITE3_OPEN_CREATE | SQLITE3_OPEN_READWRITE);

// Create a table.
$db->query(
'CREATE TABLE IF NOT EXISTS "users" (
    "id" INTEGER PRIMARY KEY AUTOINCREMENT NOT NULL,
    "name" VARCHAR
  )'
);

// Insert some sample data.
$db->query('INSERT INTO "users" ("name") VALUES ("Karl")');
$db->query('INSERT INTO "users" ("name") VALUES ("Linda")');
$db->query('INSERT INTO "users" ("name") VALUES ("John")');

// Get a count of the number of users
$userCount = $db->querySingle('SELECT COUNT(DISTINCT "id") FROM "users"');
echo("User count: $userCount\n");

// Close the connection
$db->close();

```

This example was adapted from [this gist](https://gist.github.com/bladeSk/6294d3266370868601a7d2e50285dbf5) which also includes some other handy SQLite functions.

## Running the Container

In order to run the code above, we can use Docker's PHP 7.2 image:

```bash
docker run -v $(pwd):/app php:7.2 php /app/index.php
> User count: 3
```

The user count shown is based on the number of users we created in the PHP script. If you add more users, you could see a higher number there.

### What's going on here?

- `docker run` All containers are "run" from Docker images. For more details on using `docker run`, be sure to [read the documentation](https://docs.docker.com/engine/reference/run/).
- `-v $(pwd):/app` This tells Docker to mount the current directory into the container's `/app` folder. This will help us make sure we know where our `index.php` file is inside the container.
- `php:7.2` This indicates the image we're using for this container. We could run other versions of PHP just as easily by indicating `php:7.0` or `php:5.6`.
- `php /app/index.php` Finally, this indicates the command to run inside the container. Since we mounted the `index.php` file into the `/app` directory, it's available to run there.

## Saving the Data

While this simple example works, where did our data go?

Since we didn't mount the SQLite data folder from the container onto our localhost, the data is gone when the container is gone. One simple fix for this is to change our PHP code to use a SQLite file stored in our `/app` directory. Just change the first line to:

```php
$db = new SQLite3('/app/testing.sqlite', SQLITE3_OPEN_CREATE | SQLITE3_OPEN_READWRITE);
```

And run the same Docker command as before:

```bash
docker run -v $(pwd):/app php:7.2 php /app/index.php
> User count: 3
```

Now you'll see a file (called `testings.sqlite`) in the same directory with your `index.php` file. If you run the Docker run command again, you'll get `User count: 6` as it uses the same database file as it did the first time. In this way, you can ensure that your SQLite database uses the same data every time you run your container.
