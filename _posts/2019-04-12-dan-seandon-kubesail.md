---
layout: post
title: "Dan Pastusek and Seandon Mooy on KubeSail and Simplifying Kubernetes"
author: "Karl Hughes"
categories: [interviews]
tags: [docker,kubernetes,kubesail]
---

I've been using Docker for local development and continuous integration environments for several years now, but have always struggled more with production deploys. At first, orchestration tools were almost non-existent so everyone deployed Docker differently, but now as Kubernetes has become the dominant force in production containers, it's started to get easier.

That said, Kubernetes is like learning its own new language. There's a lot to configure, you have to have a mangager and workload servers, and you will need to make sure your persistent data is safe if Kubernetes goes down. The point is, it's not trivial to deploy on Kubernetes either.

[KubeSail](https://kubesail.com/) has recently entered the container hosting space as an (almost) one-click Kubernetes cluster host. The idea is that you can define a kubeconfig file, pass it to KubeSail, and they'll get your containers up and running on their hardware. This eliminates the need to provision servers, deal with networking, TLS, and even persistent storage. You should never have to SSH into a server again!

After trying out KubeSail, I got in touch with the founders Dan Pastusek and Seandon Mooy to hear more about it, and find out what's next for the platform. They also shared a bit about their backgrounds and some tips for developers learning Docker and Kubernetes for the first time.

![](https://i.imgur.com/O6m7oUI.png)

## Interview with Dan and Seandon, Founders of KubeSail

### Describe Kubesail to me? What problem does it solve for developers?

KubeSail solves an common gripe that most hackers and tinkerers have. In my experience, **we are always launching side projects, but we don't feel like we have a go-to when it comes to deploying them.** Heroku is simple, but somewhat proprietary. It's also can be costly for running certain things, like databases and doesn’t support all our tooling. AWS is a little too complicated for the average side-project. Digital Ocean is what I often use, but I'm left with a lot of questions, like which OS do I use? What happens when I need to upgrade software or outgrow it?

**We think Kubernetes is going to eventually take over the world of deploying production software.** If you can containerize your application, then it will run anywhere. The question stops becoming how to install and manage dependencies, but where to run your container. We think Kubernetes answers that question perfectly. And KubeSail lets you try it easily, and expand if needed. The best thing about Kubernetes is portability. If my application ever outgrows the cluster I'm on, I can move my configs to another cluster easily.

### Technically, how does KubeSail work?

We designed KubeSail to be a minimal layer on top of Kubernetes. **Much like how GitHub makes Git easier and friendlier for novices and experts alike, we are trying to make Kubernetes easier and friendlier to the average developer**, while not masking or hiding any of the advanced features that make Kubernetes so popular with professional devops engineers.

So we are going to continue adding features to our UI with the goal being to make it as easy as possible to get started. But we never want to prevent you from looking under the hood. That's why we allow access to the cluster via the `kubectl` CLI tool.

Under the hood, we manage shared and dedicated clusters for free-tier and paid customers. Customers are assigned to a namespace within a cluster, and strict security policies isolate the users containers within that namespace/cluster. One of our biggest value-adds comes from the features we pack into our Clusters, which users would otherwise have to learn about and implement themselves: features like **TLS termination, custom domain routing, in-cluster persistent storage (powered by Rook), Horizontal autoscaling, security policies, maintenance, upgrades and more**. With other services like EKS, GKE, etc, the customer is simply given an entire Kubernetes cluster and left to their own devices.

### Can KubeSail be used in production environments?

**Absolutely! With the coming release of domains, you will be able to host production apps, using your domain, that are highly available, and include a load balancer, all free of charge.** We still consider it a beta product, but are becoming very comfortable managing large Kubernetes deployments at scale. We are already seeing some developers testing production workloads on KubeSail, and we think in the next few months our feature set will complete enough to be very competitive with some of the big players.

Because the first legitimate path to production is always by way of testing/dev/staging, etc, we’re focusing on capturing users who are “getting started” with Kubernetes - much like GitHub targets users who are getting started with Git. In that same vein however, we don’t believe even extremely advanced users will want to leave our service, as it will assist when scaling up to production workloads and while growing and iterating on their product.

### Why did you choose to write your [latest blog post for using KubeSail about Wordpress](https://kubesail.com/blog/wordpress-mysql-on-kubernetes)?

It demonstrates how to use most of the basic concepts of Kubernetes: Deployments, Services, Secrets, and PersistentVolumeClaims. We also know most developers have tried to launch WordPress at some point in their career, so it's a familiar reference point to compare to. Wordpress is a good demo as it has some inherent complexity of requiring a Database server, which is typically a fairly complex and scary thing to implement. If we can make MySQL easy, we can make anything easy!

### Tell me about some of the open source stuff you're working on for Kubesail, especially deploy-to-kube. This reminds me of Ziet Now in that you can have an app deployed with zero config in seconds.

Yes! We love what [Zeit](https://zeit.co/now) is doing. Zeit Now and tools like it are the inspiration for a lot of what we do. **We made [deploy-to-kube](https://github.com/kubesail/deploy-to-kube) because writing a Dockerfile and kubernetes config for a Node app takes some time and effort to get right!** We hope this helps to bootstrap Node developers with base config files that they can easily modify once they see their app running.

We have some plans to extend our open source tools to help you add things like a database, React UI, and other modules to your app, both for local development and production deployments. We think the dev/staging/deploy cycle can be so much better than it is now, without reinventing the wheel like some other technologies.

### One thing that's rather limiting about the free tier is the memory limit. What tips do you have for devs working on the KubeSail free tier?

Yeah, in fact your question got us thinking, and we agree 256mb is a bit small. [We upped it to 512mb as of today](https://kubesail.com/pricing). 😃

As for tips: it can be challenging to get certain software running with a small memory footprint, but often there are alternatives. While MySQL hovers around 400mb by default, PostgreSQL uses only ~7mb when idle. We do think there is some irony in that most vendors have zero or in fact negative interest in optimizing software - sometimes the heavier the software the higher their profit margins are. Because our value is tied to the productivity-add we offer we can work hard to make production hosting cheaper without any conflict of interest. With KubeSail, the more we can enable our users to go from “0 to 100” easily, quickly, and cheaply, the more useful we are to our customers and the more value we can add. This means our goals directly align with our customers goals - being the best way to get started with the best infrastructure.

### How long have you been doing software development? What are your primary areas of focus?

**Dan:** I have been programming most of my life. My passion has always been around UI/UX and making complex machines intuitive for humans to operate and understand. I think that shows in this project, as Kubernetes is an incredibly powerful tool, but has an extreme learning curve to become proficient with. We're trying to change that by building a layer that simplifies the basics, but doesn't take away any of the underlying power.

**Seandon:** I’ve been a sysadmin/datacenter nerd since I was a kid, and spent a good chunk of my career in large cloud-scale data-center / hosting environments. When moving from that world to the startup/software world of San Francisco, I was surprised to learn that the people building exciting apps lacked the infrastructure knowledge to execute without great expense - and the infrastructure developers were unable to produce exciting apps! DevOps as a term begins to suggest the “bringing together of two worlds”, but it falls fairly short of the dream of a single, unified industry. I am extremely passionate about unifying these knowledge sets, and Kubernetes is the most exciting opportunity since Git and Linux before it to enable a new generation of developers to build a new generation of businesses.

### What's next for Kubesail?

The list of plans gets longer each day! We are stoked about this project and the attention its received so far. We'd love to work on this project full time, but already we have the following features planned for the near future:

- Domains - work is done -- we're announcing this in the next day or two
- Embeddable YAML editor -- you can write a deployment config, and share it publicly with others who want to deploy your app(s) on their namespace.
- Free SSL certificates w/ auto-renew -- because certbot automation is still a little harder than it needs to be
- Templates for common tools: Docker Registry, logs, etc

### What books or resources do you recommend to developers who are new to Kubernetes?

- [The Kubernetes docs](http://kubernetes.io) are actually pretty helpful, if a bit dense in parts.
- Community sites like [Kubedex](https://kubedex.com/) and [Kubernetes the Hard Way](https://github.com/kelseyhightower/kubernetes-the-hard-way).
- Get lots and lots of ecosystem experience (Kubernetes builds on existing topics like Docker).
- [KubeSail](https://kubesail.com/)! We are committed to releasing tools and content that make it dead simple to deploy apps using the latest and greatest infrastructure. This question having no good answer is why we believe we have so much to offer.
