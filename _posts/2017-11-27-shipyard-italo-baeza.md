---
layout: post
title: "Interview with Shipyard's Creator, Italo Baeza"
author: "Karl Hughes"
categories: [interviews]
tags: [docker,php,shipyard]
---

Over the past month, I've been talking to other developers who work with PHP and Docker about the tools and resources they use to make managing Docker easier. This week, I'm sharing a short interview with Italo Baeza, the creator of [Laravel Shipyard](https://github.com/DarkGhostHunter/Shipyard), an open source project that helps Laravel developers (and really all PHP developers) get a whole local environment set up using Docker. Like [Laradock](/blog/2017/mahmoud-zalt-laradock), Shipyard abstracts away most of the Docker complexity and makes running containers secondary to writing code, but Shipyard is a bit simpler.

There are a number of advantages to Shipyard's Docker-based approach, but probably the biggest is ease of use. As a developer, you don't want to get bogged down in complex environmental configuration, so tools that quickly set up your database, server, and ancillary services are usually a good choice. Shipyard is a great tool for this purpose, and it comes with [more detailed documentation](https://darkghosthunter.github.io/Shipyard/) if you'd like to learn more.

In this interview, I asked Italo about his reasons for creating the project as well as some of Shipyard's limitations and future features.

![](https://i.imgur.com/mi3Cfvs.png)

## Interview with Italo Baeza, creator of Shipyard

### How long have you been working in PHP? How about Docker?

Since 2005. Actively from 2012. I started working in Docker in January, moments after intensely cursing to my computer after seeing the Vagrant images were so big that I couldn't play a [Laracast](https://laracasts.com/) video while waiting.

### How did Shipyard come about? Did it start off as in internal project or was it always open source?

After downloading [Laradock](http://laradock.io/) - kudos to those guys - I asked to myself "Why there was so many tools that probably won't be usable for a normal project?". After pointing out the advantages of using a Docker-based environment to my lazy boss, I used a full week to make it from the ground up. I thought that more people would want a simple PHP-development with common tools without being too convoluted or complex, so I uploaded it to Github.

### Briefly, how does Shipyard work?

Download, run the script, dock and forget.

Shipyard uses Docker to instantiate NGINX, PHP, MySQL and so on independently from the host system, while adding a separate "Warehouse" Container to run NPM and Artisan commands. Also, it uses [OpenSSL](https://www.openssl.org/) to automatically make SSL certificates to use internally, so no more "How do I make my development project compatible with this HTTPS-thing", and applies some specific Docker fixes on Windows or MacOS. All of these task are made thanks to these shell scripts. 

### What is the "warehouse" container exactly? Why did you choose to include it in Shipyard?

It's gonna sound very silly, but it's the hard cold truth: I was f*cking tired of installing NPM and PHP or whatever on my PC, and manually updating them.

Instead I thought, "Wouldn't be nice to have a container with all of these tools, that would work even if I go nuts and buy a Mac?" Minutes after, Warehouse was born. It has a lot more than NPM and PHP however I decided to not include the whole Internet set of tools under it.

Basically, you enter to the container using docker exec and unleash the power of your commands like in any other shell. You don't have to install even [Composer](https://getcomposer.org/) in your computer to use composer commands inside Warehouse.

### You mention in the docs that Shipyard is not suitable for production deploys. How do you deploy your containers?

As of today, we are using containers only for local dev - we are still under the road to push to v1.0. When the time comes, we will see if Docker comes as an option or a hard VM will suffice to simplicity. So, as long the final deployment is not made, Shipyard lives with the promise of aiding development.

Also, we thought that the deployment step may vary depending of the users - some may need a VM for cost or familiarity reasons. Also, deploying Docker to production it's not easy, there is a extensive list of things to care about to make it reliable and secure. Shipyard does nothing for this last steps, that's why it has a very big warning that can be summed up in one phrase: **do not use Shipyard for production, you are gonna have a very, very bad time.**

### What future plans do you have for the project? I see you've put in a few feature requests in the issues. Are any of these in progress or are you looking for contributors? 

Both. While I'm working in other tasks at the moment, I leave these feature request and issues to know what to do next once I'm relatively free.  As for now, it works™.

If there are contributors willing to make these implementations before I put my hands on it, good. But it's not the main focus for me right now.

### What resources do you recommend for PHP devs who want to learn Docker?

For starters, Chris Fidao has a nice [video series for Docker in Development](https://serversforhackers.com/s/docker-in-development), and from there, [Shipping Docker](https://serversforhackers.com/shipping-docker) could be the next course to deployment (I'm looking to hook in it when we move to production) for advanced users. From there, the road gets as rocky as you want to be. You can even remake your own version of Shipyard to suit your needs, and learning why Alpine is good for tiny things, and an Ubuntu base image for others.

The Good things about Docker is that installing and learning to dockerize a folder, a tiny Alpine image or a full Ubuntu image doesn't mess with the host system, so you can make and rebuild without fearing to break something or downloads tons of gigabytes.
