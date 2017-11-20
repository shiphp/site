---
layout: post
title: "Ben Maggacis on Codemason and Deploying with Docker"
author: "Karl Hughes"
categories: [interviews]
tags: [docker,php,codemason]
---

Once PHP developers get familiar with the basics of local development with Docker, the next thing they always ask me about is deploying their projects. Running containers in production is a complicated problem, and because each project is different, there's not just one solution that works for everyone. I've written before about [using Hyper.sh](https://www.shiphp.com/blog/2017/deploying-php-hyper-sh), but I've also used [Amazon's EC2](https://aws.amazon.com/ecs/) and [Rancher](http://rancher.com/).

But, there's one new option that I recently discovered that might be the most straightforward Docker hosting platform I've seen: [Codemason](https://codemason.io/). With Codemason, the configuration is minimal, and after you connect your own server (it takes about 3 minutes to set one up with [DigitalOcean](https://m.do.co/c/888fefc32a01)), Codemason can have your app deployed in a matter of minutes without doing anything on the command line. While I've gotten very comfortable operating in the terminal, I can appreciate a good GUI because it allows other developers to more quickly see what you've done and how everything works.

After using Codemason, I got to ask Ben Maggacis, the project's founder, some questions about it. Below are some of Ben's insights into how Codemason works and tips for PHP developers trying to learn Docker.

![](https://i.imgur.com/IEAbbFm.png)

## Interview with Ben Maggacis, creator of Codemason

### How long have you been working with Docker? What languages do you primarily program in?

I've been working with Docker in some form for at least 2 years now. After reading about it a few times, when I needed to setup my development environment for a new project, I decided to try it out.

The main thing I remember was how easy it was to use after initial learning curve. Being able to spin all kinds of software up and to have it almost immediately working was like magic. I think as developers, it's especially exciting to us when something comes along and surprises you like that. After that experience, I was convinced. I understood what the excitement was about and have been using Docker ever since. 

Eventually my interest in Docker led me starting [Codemason](https://codemason.io/), a service that simplifies building, deploying, running and scaling container-based applications. 

I mainly work in PHP and JavaScript with Laravel, Nodejs and Vuejs. In my opinion, Laravel and Vuejs are frameworks that completely understand the importance of the developer experience.

### Are you working full time on CodeMason or is it a side project?

I love working on Codemason and I balance it with my other commitments. There's an entire world of overcomplicated DevOp problems that get in the way of developers building their apps and I get to hunt them down turn them into something trivial for our users.

Plus, to me, there's just something really satisfying about writing code that controls infrastructure and containers, running it and watching (eventually) run perfectly — you get to create that magic feeling.

### Tell me about how Codemason came about?

The idea for Codemason came about when I was working on another project that used Docker. By then, I was a bit more familiar with Docker and had started get more opinionated about how I thought things should be done - how Dockerfiles should be structured, what should be in containers, etc.

Eventually it got to a point where I started seeing all these over-complications of obstacles everywhere within the  "DevOps" side of things. I'd look up these obstacles and see that other developers all faced them and each them could be solved in about a dozen different ways. It seemed strange that we were all trying to solve the same problems in our own different ways, especially when it so much of it can be consolidated.

It was always planned as a software as a service offering but I intended to use it on a couple of my own projects first to make sure it was something really useful.

### Are there any primary use cases for Codemason or do you see it being widely usable for any kind of Docker hosting?

Codemason is widely usable for any kind of Docker hosting. While we definitely have a "Codemason way" of doing things and a soft spot for web apps, it can handle any container-based project.

The "Codemason way" is our opinionated approach to deploying, managing and scaling. By being opinionated, we can make decisions about things so our users don't have to — the last thing we wanted to do was to create another service/tool that left developers with more questions than answers. We want to free them up to spend more time on the things that create real value for their apps/business. 

That’s why we created Craft Kits. You can use a Craft Kit to turn any app into a Docker app with one command (`mason craft php`), you don't even need to know Docker and and you can even swap out the services a Craft Kit uses with ease (`mason craft php --with="php, mysql"`). They’re available for everyone to use, even if you're not on Codemason but the official ones, because they're opinionated, work seamlessly with the Codemason deployment process.

### Technically, how does Codemason work?

Codemason is very robust. Behind the scenes we use a variety of open source projects that power a lot of the functionality which we tie together with the dashboard in a totally seamless way.

- **Docker** - Everything that Codemason runs on your server is a container. Because it's containerised, we don't need to know what's inside, we can just worry about running it
- **Rancher** - Codemason wraps Rancher which does a lot of the heavy lifting when it comes to orchestrating Docker containers 
- **GitLab** - We integrate with GitLab's repositories (pushing your code to Codemason), CI runner (building your image) and the built-in private image registry.

We keep a list of key technologies and open source projects that help Codemason on our [Thank You page](https://codemason.io/thanks).

The Codemason dashboard is built with [Laravel](https://laravel.com/) and [Spark])(https://spark.laravel.com/) on the backend and [Vuejs](https://vuejs.org/) on the frontend. Spark is a Laravel package that provides scaffolding for SaaS applications - it's been great so far and has saved a considerable amount of time. I also extended Spark to support multiple subscriptions which we use for to bill for our managed add-ons. I'll be publishing a blog post explaining how to extend Spark to support multiple subscriptions soon.

The other key part of Codemason is the [Mason CLI](https://codemason.io/docs/master/creating-apps). The Mason CLI is a completely open source command line tool built with Node.js that you can use to improve your development and deployment experience.

### What future plans do you have for the project?

I want to keep improving and expanding on Codemason. I am really happy with what we have so far, it works great, it looks great and the developer experience is excellent but there's so much more we can do! There is an entire world of DevOps obstacles that can be cleared out of the way for developers.

We're going to be focusing on expanding the existing build system to make it a full featured CI/CD pipeline, adding more managed add-ons and continuing to make it even easier to get ahead of scalability issues.

### What resources do you recommend for devs who want to learn Docker (either starting out or advanced)? Any books, blogs, or articles you've found helpful?

The [official Docker docs](https://docs.docker.com/get-started/) are actually pretty useful and worth checking out if you're new to Docker but they're a pretty dense read. So for people just getting started with Docker, I'd recommend checking out [Runnable's slash Docker project](https://runnable.com/docker/). It's a series of posts that cover basically everything you need to know about Docker. They cover a couple of different languages not just PHP. 

When I first started playing with Docker, there wasn't too much information about building PHP applications with Docker so I mostly learned from tutorials written for other languages and adapted that back to PHP. Fortunately, thanks to projects like Shiphp, that information is being made readily available for PHP developers, so it's especially easy for people get started.

If you really want to immerse yourself in Docker, don't shy away from Docker tutorials that are in different programming languages. For example, this [Docker for Java developers ebook](http://www.oreilly.com/programming/free/files/docker-for-java-developers.pdf) is Java focused but a lot of the concepts it covers are transferable.

Finally, if you're wanting to start looking into more DevOps related concepts, the team at [Rancher](https://rancher.com/) frequently do a lot of interesting webinars and blog posts which are also worth checking out.
