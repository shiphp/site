---
layout: post
title: "Interview with Mahmoud Zalt, Creator of Laradock"
author: "Karl Hughes"
categories: [interviews]
tags: [docker,php,laradock]
---

In the process of writing [Building PHP Applications in Docker](/books), I ran across a number of great tools and resources for PHP developers who want to get started using Docker. One tool that I've since started using for some of my side projects is [Laradock](http://laradock.io/), a docker-based development environment for PHP applications.

Many PHP developers are used to virtual machines, which allow them to set up all the necessary services (eg: databases, caches, application servers, etc.) at once, but things aren't quite so simple when moving to Docker. Laradock gives you a package of Dockerized services that are commonly used in PHP projects and lets you get started with Docker without writing a single `docker run` command. It's pretty amazing.

## Interview with Mahmoud Zalt, Creator of Laradock

Last week, I got to interview Mahmoud Zalt, the creator and maintainer of Laradock to hear about his experience with Docker, PHP APIs, and dig into some of the use cases for his Docker-based tool. If you want to learn more about Mahmoud's work, be sure to [check out his website](https://zalt.me/) as well as his [Github profile](https://github.com/Mahmoudz).

![](https://i.imgur.com/f3jgs4X.png)

### How long have you been using Docker and PHP?

I started my career as a Java desktop Apps developer and got introduced to PHP in late 2012, after joining a startup that uses it for its web services. I hated PHP at the beginning, but after discovering Laravel in early 2013 I started accepting it.

I've been using Docker since mid-2014. I fell in love with it after the first try. I was impressed by its booting speed compared to Vagrant, and the flexibility it gave me to switch between different versions of the same software.

### How did Laradock get started?

In the early days of Docker, booting multiple containers wasn't an enjoyable process.
I used to have scripts to automate booting multiple containers until [Docker Compose](https://docs.docker.com/compose/) showed up and solved that problem.

First I started looking for some open source, pre-configured docker images as a ready Docker environment for my personal PHP Apps. For some reason, the existing solutions used to contain all the software (PHP, MySQL, NGINX, etc.) in a single container, which prevents benefiting from the real power of Docker.

So I had to build my own solution, which consists of multiple separated Docker images that can be booted by a single `docker-compose` command to serve all my PHP Apps with the fewest modifications.

Before attending Laracon EU 2015, I wanted to make a new open source project for the Laravel community. Just four days before the conference, I released the first version of Laradock, as an alternative to Laravel Homestead for the Docker users. I remember pitching it to [Taylor Otwell](https://twitter.com/taylorotwell) offstage, who liked it, although he hadn't had a chance to play with Docker before.

Later, due to the large adoption from the PHP community, I modified Laradock to support more PHP projects as well. I would like to take this opportunity to thank all the contributors and the awesome maintainers of Laradock, for making this project as cool as it is today.

### What's Laradock's intended audience and what it is typically used for?

My short answer would be "Laradock is for PHP developers of any level who want to setup their development environment, using Docker without learning about it."

### Your documentation is very thorough. Did you focus on documentation much in the beginning or only after Laradock gained some popularity?

I focused on writing a super simple and clear documentation from day one. My approach for writing documentation is trying to make them serve as tutorials about the topic, in addition to being a guide for how to use the tool. The Laradock documentation used to be a simplified Docker tutorial for beginners.

Therefore many users have told me that they learned how Docker works because of the Laradock documentation, which for them was simpler than the official Docker documentation. That official documentation was intended for DevOps engineers, unlike the Laradock documentation which was intended only for PHP Developers.

Later on, many contributors helped to grow and enhance the documentation to become what you see today.

### Is there a good way to share Laradock configurations with other maintainers of a project?

Sharing the App environment with other team members is very helpful and good approach. You can do that with Laradock since it can be used in two ways: one is setting up one Laradock per project and the other is setting up single Laradock for all projects.

To share the environment with other developers you'll need to use the single project setup (one Laradock per project), which adds the Laradock folder to the root of your application, thus making it part of the same repository of your project.

### You also maintain a PHP framework called [Apiato](https://github.com/apiato/apiato). Tell me a little about that.

Apiato is my favorite open source project so far and where I spend most of my time contributing. It is a framework for building API-Centric Apps with PHP and it is built on top of the latest stable version of Laravel.

The project contains a great list of helpful features, for API developers. Some of the features are:
 
 - Quick authentication with OAuth 2.0
 - Auto documentation generator
 - Easy API versioning
 - Exception formatters
 - ETag, CORS, and query parameters
 - API test helpers
 - Auto ID encoding/deciding
 - Role-based access control
 - Auto CRUD endpoints generator
 - And much more

The project was first built on top of Laravel 5.1, which didn't have great support for building API's, so I found myself doing a lot of modifications to Laravel and configuring multiple packages over and over for each new API project.

I decided to build my own starter project to speed up that process, and in April 2016 Apiato was first released, it was named Hello API (Hello World of an API), and then renamed to Apiato for better SEO.

The project was also an opportunity for me to collect feedbacks about a unique software architecture called [Porto](https://github.com/Mahmoudz/Porto), that I've been developing for nearly 3 years, and I am quite happy with the results so far.

I also would like to take the opportunity to thank all the contributors of Apiato, especially [Johan Alvarez](https://github.com/llstarscreamll) and [Johannes Schobel](https://github.com/johannesschobel), for their support and making this project super awesome.

### What tips do you have for PHP developers who want to learn Docker? Are there any resources you recommend?

My approach for learning Docker quickly was deleting all my programming tools from my machine and completely relying on Docker to execute and serve my code. Without Docker, I cannot run a single line of code, which forces me to deal with it on a daily basis.

Nowadays, there are hundreds of tutorials, screencasts, books, and articles about Docker, I'll leave it to the developers to choose what works best for them. However, I would recommend the [official Docker documentation](https://docs.docker.com/) as the most trusted reference.

If you are completely new to the virtual containers concept and Docker in particular, then give [Laradock](http://laradock.io/) a try, it will definitely help you get started.
