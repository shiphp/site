---
layout: post
title: "Interview with Luis Pabón: Creator of PHPDocker.io"
author: "Karl Hughes"
categories: [interviews]
tags: [docker,php,phpdocker.io]
---

As I've been learning more about the PHP and Docker ecosystem, I've been keeping an eye out for tools that PHP developers can use to speed up or improve their Docker workflows. [PHPDocker.io](https://phpdocker.io/) is one such tool, and this week I got to interview the project's creator, Luis Pabón.

If you're not familiar with it, PHPDocker.io is a free and open source tool that will help you craft a Docker environment specifically for your PHP project. Instead of abstracting away Docker as some tools do, Luis chose to make a tool that helps you with one of the hardest parts of your Docker setup: creating Dockerfiles and Docker Compose configurations.
 
The primary product (an [online Docker Compose and Dockerfile generator](https://phpdocker.io/generator)) asks you a few key questions about your environment and then automatically creates a project template that you can either copy into an existing project or use as a boilerplate for a brand new Dockerized PHP project.

![](https://i.imgur.com/nIqHN4r.png)

## Interview with Luis Pabón of PHPDocker.io

### How long have you been working in PHP? How about Docker?

I've been working in PHP, professionally since 2003, but well before that since university. Docker is of course a lot more recent, my first contact was in 2013.

### What's your primary job?

I'm a freelance software developer in the UK. I've worked for all sorts of companies, my most recent client being BBC Worldwide. This was the first gig in which we worked with Docker from day one as per my recommendations at a project that was a testbed of a new technology stack including also Kubernetes. I'm introducing the use of docker-compose for development environments (with a lot of lessons learned from PHPDocker.io) to my current client.

### Tell me about how PHPDocker.io came about?

Some time around 2013 I came into a greenfield project where people had been trying containers as a way of both deployment, and local development. Coming from the world of Vagrant and endless Puppet or Chef provisioning runs, this was a total eye opener. Environments with Docker started fast, using fewer resources, and it became possible to run a whole technology stack for a product locally. You weren't limited anymore to specific versions of PHP, Node or any other part of the technology stack - this was also around the time PHP 7 came out with all the new language features and performance boosts, and if you've worked with traditional deployment environments, it's always a big palaver to get version bumps of your runtime packages.

I started then to experiment with building different environments and quickly realised this was a very easy to automate generation of. During downtime between contracts and wrote a proof of concept of the site. At the time it was a private project as it was so rough, but eventually I felt I should really open it up, which I did, [releasing it on Github with the Apache 2.0 license](https://github.com/phpdocker-io/phpdocker.io). 

### How does PHPDocker work?

It really is a very simple site. I built it using the full stack [Symfony PHP framework](https://symfony.com/) as I intended to take advantage of all the good stuff it has out of the box, including form handling which is obviously the centerpiece of the whole application. The framework specific code deals with request/response handling (including validation), form handling as well as rendeding the actual user interface.

The generator code itself, which is not tied to the framework, is simply a load of business logic around generating files out of templates using data objects that describe the user's choices. The UI relies heavily on javascript for UX purposes.

Most of the work has always been getting the generated environments to actually work first time when dropped into a project. The challenges are diverse: Silex and Symfony <= 3.4 use different front controllers than Laravel, Symfony 4 and Slim; Phalcon requires an additional PHP extension to function; this sort of thing. I have a load of projects set up that I can drop environments on to test, and I have to make sure all different PHP versions work with these.

There's also a simple news system [on the homepage](https://phpdocker.io/), again taking advantage of Symfony and available bundles - it took a whole 1 hour to implement, admin panel included.

PHPDocker.io is your typical case of a proof of concept made a permanent fixture.

Hosting-wise, I have a small Kubernetes cluster running on AWS that among other things runs PHPDocker.io, deployed via Ubuntu Juju.

### For PHPDocker, you maintain your own PHP Docker images. Why did you decide to do that instead of extending PHP's official base images?

I originally was using [the official PHP images](https://hub.docker.com/_/php/), but could never really get them to work right. The main hurdle is that it requires extensions to be compiled. The images come with tools to install these, but they don't install their build dependencies, which I had to hunt down and also add to the Dockerfiles to be installed then cleaned up after the extensions were compiled. You can imagine how long it takes to build one of these and how large the resulting images are since all the build tools are present on the base php image (I've just checked, php:7.1.11 is 371MB). The process is also prone to breakage and there are many popular extensions (Redis, for instance) that are not available in the official PHP images. 

There's also an issue to how PHP-FPM dumps its logging. By default it logs to the filesystem, and the messages come wrapped on PHP-FPM specific lingo that was polluting any logs coming out of the containers. There's also other small bits and pieces required for container operation that surprisingly the PHP team has never dealt with.

So I changed strategy. I use system-packaged PHP versions when available, and for bleeding edge versions not yet available I resort to a reputable repository. Currently, the PHP 5.6 image uses Debian Jessie. 7.0 is also Debian Jessie with packages from Dotdeb.org, and 7.1 and 7.2 uses Ubuntu and Ondřej Surý (who's the php packager for Debian). 

As a side note, I considered [Alpine](https://alpinelinux.org/) instead of Debian/Ubuntu, but quickly run into trouble due to how Alpine implements its `libc` analog, and not `libc` itself (plenty of issues with networking).

With this in mind, I built all the base images users could take as a starting point. This allowed for a few things: each PHP version would always be the latest on their minor version line, the logging problem was fixed, PHP is properly configured for containers, and I was freed up to concentrate in other things.

I might revisit this soon, but this has proved to be the best solution. Traffic to the site is surprisingly high and constant, there's a lot of people out there using this on a daily basis, and rarely do I receive bug reports or feature requests on the images.

### Are the configurations generated by PHPDocker Appropriate for deploying to a hosted Swarm environment?

The environment is geared towards local development: as such, there are volume mounts for your code and containers run your code from these mounts. Containers aren't built from your code, which you'd need for deployments as well. You can however take all this config as a starting point to creating your deployments, with the confidence that you're using the exact same runtime as for development.

I have found through experience that `docker-compose` environments are a very good match to Kubernetes, and if you craft it right, rarely will you run into environment issues. This is made possible because in both cases you use the same runtimes, and network access is completely abstracted out.

You can also use the base images as your base to build your app. They're public, free, based on solid tech, and already tweaked to play well with containers.

### What future plans do you have for the project?

I always consider [feature requests as they come](https://github.com/phpdocker-io/phpdocker.io/issues), but I do not always have much time to implement them as I work full time. Some of these are harder than others. For instance, Apache is harder to configure than Nginx, and you can deploy PHP in two ways instead of one (`mod_php` vs `fcgi/php-fpm`) - I still need to think how can this work in a manner that'll be good for the Apache users out there.

I'm always open to [pull requests](https://github.com/phpdocker-io/phpdocker.io/pulls) however :)

PHPDocker.io is not really meant to be a tool that allows you to replicate every possible use case in the same way other, more mature, approaches to development environments like [Puphpet](https://puphpet.com/). Since the configuration generated is so simple, it's easy for users to simply add any extra stuff by hand simply by following the patterns in there.

Beyond that, the UI itself needs a refresh. I'm useless as a visual designer so any help is appreciated.

### What resources do you recommend for PHP devs who want to learn Docker?

There's plenty out there. The first thing would be having a look at the generated environment and how it's put together, including the base images. Always try to learn how the technology stack you use is cobbled up together, fixing issues in there is always part of the job.

Definitely have a look at [these Docker best practices](https://docs.docker.com/engine/userguide/eng-image/dockerfile_best-practices/) before you start building your images for deployment. There's a lot of stuff in there, not obvious on the surface, that you need to know to streamline your images, like for instance how Docker images are layered on a per-statement basis - this will allow you to optimise and reduce your build times and subsequent deployments considerably.

Learn also other uses of docker images. I don't have a great deal of dev dependencies installed directly on my laptop, instead I use docker run commands.

Write up your applications in a manner that they run well containerised. [12-factor app](https://12factor.net/) is a good read to get you thinking in this direction.

Definitely clue yourself in to [Kubernetes](https://kubernetes.io/), it's taking the lead over Mesos, Swarm and ECS for orchestration. You can play with it easily without going blind with [Minikube](https://kubernetes.io/docs/getting-started-guides/minikube/).
