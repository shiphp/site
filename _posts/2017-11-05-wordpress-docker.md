---
layout: post
title: "Running Wordpress with Docker Containers"
author: "Karl Hughes"
categories: [tutorials]
tags: [php,docker,intermediate,wordpress]
---

The first exposure I had to PHP code was [Wordpress](https://wordpress.org/),
and ever since then the CMS has held a soft spot in my heart. Wordpress is used
by millions of websites, so knowing how to set up a new instance and run it
within Docker containers is a valuable skill for PHP developers.

This tutorial will use the [official Wordpress Docker
image](https://hub.docker.com/_/wordpress/) as well as some tricks we learned in
a previous tutorial about [using PHP and MySQL in
containers](https://medium.com/shiphp/using-docker-to-run-a-php-and-mysql-application-b89f89098cc5).
Let’s get started!

### 1. Starting up a Database Container

Before we run Wordpress, let’s get a MySQL instance started so we can link it to
our Wordpress container. As we explained in [a previous
tutorial](https://medium.com/shiphp/using-docker-to-run-a-php-and-mysql-application-b89f89098cc5),
we’ll use MySQL 5 and a non-root user for the database. The only additional
option we are passing in for this example is a database name (see `-e
MYSQL_DATABASE=wordpress`).

    docker run -d --name database -e 
    =admin -e 
    =P^RKvF -e MYSQL_RANDOM_ROOT_PASSWORD=true -e 
     mysql:5

MySQL is now running, and you can verify it by running `docker ps`.

### 2. Running Wordpress

Installing Wordpress normally means downloading the core library, updating
configuration files, and setting up a webserver, but Docker makes this process
into a one-line command!

    docker run --rm --name wp-local --link database:mysql -e WORDPRESS_DB_USER=admin -e WORDPRESS_DB_PASSWORD=P^RKvF -e WORDPRESS_DB_NAME=wordpress -p 8080:80 wordpress

When you run the above command, you should see some terminal output, but once
that slows down, head over to `http://localhost:8080/` and check it out.
Wordpress is ready to finish its installation.

![](https://i.imgur.com/ntev38S.png)

*Once the Wordpress container is started you can complete the installation at
http://localhost:8080*

#### What’s going on here?

Let’s dig into this Docker command in more detail so you can optimize it for
your own use later.

* `docker run` is Docker’s command to run a new container.
* `--rm` will remove the container after it shuts down. Note that if you don’t
save your files in a volume, you may want to keep the container so you can
restart it later.
* `--name wp-local` is a name for this container. You can choose anything you
like, but it helps us identify the container if we want to log into it later.
* `--link database:mysql` tells the new Wordpress container to link to the
existing `database` container, but to map it to the hostname `mysql`. This is
the default database host name.
* `-e WORDPRESS_DB_USER=... -e WORDPRESS_DB_PASSWORD=... -e WORDPRESS_DB_NAME=...`
sets environmental variables for our database connection in the Wordpress
container. You could also use a `.env` file (see [section 2 in this
article](https://medium.com/shiphp/environmental-variables-in-php-and-docker-cae58177e679)
for details).
* `wordpress` is the name of the official Docker image for Wordpress. More
environmental options can be found in [their documentation on Docker
Hub](https://hub.docker.com/_/wordpress/).

### 3. Advanced Usage

Now that you’ve got a simple example running, you can shut it down and set some
advanced options.

#### Running the Container in Detached Mode

To run the above Docker `run` command in “detached” mode (meaning you can do
other things in your terminal while it’s running, simply add the `-d` flag
anywhere to your `docker run` command. For example:

    docker run 
     --rm --name wp-local --link database:mysql -e WORDPRESS_DB_USER=admin -e WORDPRESS_DB_PASSWORD=P^RKvF -e WORDPRESS_DB_NAME=wordpress -p 8080:80 wordpress

#### Using a Volume for File Uploads

You may be wondering what happens if a user uploads a file. Well, the file gets
uploaded to the container, but it’s not visible anywhere in your host system. We
can fix that by mounting Wordpress’ file upload directory as a volume:

    docker run -d --rm --name wp-local --link database:mysql -e WORDPRESS_DB_USER=admin -e WORDPRESS_DB_PASSWORD=P^RKvF -e WORDPRESS_DB_NAME=wordpress 
     -p 8080:80 wordpress

The new part of this command mounts a volume from our local filesystem into the
Wordpress container and vice-versa. Now when we upload a file in our local
Wordpress instance it should show up in the `/wp-content/uploads` directory on
our host machine.

#### Using a Volume for Plugins and Themes

Similarly, we can mount the whole `wp-content` directory to keep plugins,
themes, and uploads synced on our local machine and in our container. It just
takes a slight modification to the above command:

    docker run -d --rm --name wp-local --link database:mysql -e WORDPRESS_DB_USER=admin -e WORDPRESS_DB_PASSWORD=P^RKvF -e WORDPRESS_DB_NAME=wordpress 
     -p 8080:80 wordpress

Now any changes we make to any plugins on our local system or any new themes we
add will show up in the running Wordpress container.

As you can see, this method of setup can actually make installing and
configuring new instances of Wordpress much faster and even safer. Because devs
cannot modify core files, it can prevent consistency problems and make upgrading
Wordpress simpler.
