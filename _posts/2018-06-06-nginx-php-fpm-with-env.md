---
layout: post
title: "Building an NGinx and PHP FPM Docker Image with an Environment Variable as a Hostname"
author: "Karl Hughes"
categories: [tutorials]
tags: [nginx,php-fpm,docker,env]
---

Lately I've been working to deploy a suite of PHP microservices using Docker containers. One of the problems is that our PHP applications are set up to work with PHP-FPM and Nginx (instead of the admittedly simpler [Apache/PHP setup covered here](https://www.shiphp.com/blog/2017/php-web-app-in-docker)), so each PHP microservice needs two containers (and by extension, two Docker images): a PHP-FPM container and an NGinx container.

The app runs over half a dozen services, so if you multiply that out, it will be 30 or so containers between dev and prod. Rather than build unique NGinx images for each one, I decided to build a single NGinx Docker image that would take the PHP-FPM host name as an environmental variable and run a unique configuration file with it.

![](https://i.imgur.com/XoCNwnk.jpg)

In this blog post, I'll outline my journey for going from Method 1 to Method 2 above, and finally end it with the solution I came to using a new custom Docker image. I've made [this image open source](https://github.com/shiphp/nginx-env), so if this is a problem you're familiar with, feel free to check that out. 

## Why NGinx?

Using NGinx with PHP-FPM [can yield better performance for PHP apps](https://blog.a2o.si/2009/06/24/apache-mod_php-compared-to-nginx-php-fpm/), but the downside is that the [PHP-FPM Docker image](https://hub.docker.com/_/php/) isn't bundled with NGinx by default like the PHP Apache image is. If you want to connect an NGinx container to a PHP-FPM backend, you need to add the DNS record for that backend into your NGinx config. For example, if the PHP-FPM container were running as a container named `php-fpm-api`, then your NGinx configuration file should have this in it:

```nginx
    location ~ \.php$ {
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        # This line passes requests through to the PHP-FPM container
        fastcgi_pass php-fpm-api:9000;
        fastcgi_index index.php;
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_param PATH_INFO $fastcgi_path_info;
    }
```

Hard-writing the name of the container into your NGinx config is fine if you're only serving a single NGinx container, but as I mentioned above, we need to run several NGinx containers - one for each of our PHP services. Creating a new NGinx image (that we later have to maintain and upgrade) would be a pain, and even managing a bunch of different volumes seemed like a lot of work for changing a single variable name.

## The first solution: use Docker's documented method

Initially, I thought this would be easy. There's [a great little section about how to do this using `envsubst` in the Docker docs](https://docs.docker.com/samples/library/nginx/#using-environment-variables-in-nginx-configuration), but unfortunately, this didn't work for my NGinx config file:

#### vhosts.conf
```nginx
server {
    listen 80;
    index index.php index.html;
    root /var/www/public;
    client_max_body_size 32M;

    location / {
        try_files $uri /index.php?$args;
    }

    location ~ \.php$ {
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_pass ${NGINX_HOST}:9000;
        fastcgi_index index.php;
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_param PATH_INFO $fastcgi_path_info;
    }
}
```

My `vhosts.conf` file takes advantage of several [built in NGinx variables](http://nginx.org/en/docs/varindex.html), so when I ran the Docker command outlined in the docs (`/bin/bash -c "envsubst < /etc/nginx/conf.d/mysite.template > /etc/nginx/conf.d/default.conf && nginx -g 'daemon off;'"`), I got errors complaining that `$uri` and `$fastcgi_script_name` weren't defined. These variables are normally passed in by NGinx, so I couldn't easily figure out what they were and pass them in myself, plus that would make my container less dynamic.

## Another Docker image to the rescue...almost

Next, I went searching for a different NGinx base image. Two came up, but neither had been updated in a couple years. I started with [martin/nginx](https://hub.docker.com/r/martin/nginx/), and tried to see if I could get a prototype working.

Martin's image is a little bit unconventional because it requires a specific folder structure. In the root, I added a `Dockerfile`:

```Dockerfile
FROM martin/nginx
```

Next, I added an `app/` directory that was empty and a `conf/` directory with a single file called `vhosts.conf`:

```nginx
server {
    listen 80;
    index index.php index.html;
    root /var/www/public;
    client_max_body_size 32M;

    location / {
        try_files $uri /index.php?$args;
    }

    location ~ \.php$ {
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_pass $ENV{"NGINX_HOST"}:9000;
        fastcgi_index index.php;
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_param PATH_INFO $fastcgi_path_info;
    }
}
```

This is pretty much the same as my original configuration file with this one line changed: `fastcgi_pass $ENV{"NGINX_HOST"}:9000;`. Now when I wanted to start an NGinx container with a PHP container named `php-fpm-api`, I could build a new image and run it with an environmental variable:

```bash
docker build -t shiphp/nginx-env:test .
docker run -it --rm -e NGINX_HOST=php-fpm-api shiphp/nginx-env:test
```

It worked! But, two things bothered me about this method:

1. The base NGinx image being used is over two years old. This could pose security and performance risks.
2. Having an empty `/app` directory seems like an unnecessary requirement plus my files will be stored in a different directory.

## The final solution

I figured Martin's image would be a good place to start for a custom solution, so I [forked the project](https://github.com/shiphp/nginx-env) and built a [new NGinx base image that fixes the two issues](https://hub.docker.com/r/shiphp/nginx-env/). Now, if you want to run a dynamically-named backend with an NGinx container, you can simply do this:

```bash
# Pull down the latest from Docker Hub
docker pull shiphp/nginx-env:latest

# Run a PHP container named "php-fpm-api"
docker run --name php-fpm-api -v $(pwd):/var/www php:fpm

# Start this NGinx container linked to the PHP-FPM container
docker run --link php-fpm-api -e NGINX_HOST=php-fpm-api shiphp/nginx-env
```

If you want to customize the image by adding your own files or NGinx configuration files, then just extend it with a Dockerfile:

```Dockerfile
FROM shiphp/nginx-env

ONBUILD ADD <PATH_TO_YOUR_CONFIGS> /etc/nginx/conf.d/

...
```

Now all my PHP-FPM containers use their own instance of a single Docker image, making my life much easier when it comes time to upgrade NGinx, change permissions, or tweak something.

[All the code is available on Github](https://github.com/shiphp/nginx-env), so if you see any problems or want to suggest improvements, feel free to create an issue. If you have questions with this or anything else Docker-related [find me on Twitter](https://twitter.com/shiphpnow) to continue the conversation.
