---
layout: post
title: "Debugging Intermittent Test Failures with Bash and PHPUnit"
author: "Karl Hughes"
categories: [tutorials]
tags: [php,testing,bash,phpunit]
---

If you've been [writing PHPUnit tests](https://www.shiphp.com/blog/2017/phpunit-docker) for long, you've probably run into a time when a test works 90% of the time, but every now and then it throws an unexpected error or failure. If it happens only rarely, you might just get around it by re-running your test suite, but if you've got a large test suite or intermittent failures become really common, you probably need to address the issue. Here's a quick tip for debugging tests like this.

![](https://i.imgur.com/5eAdEZ9.jpg)

If you want to run your PHPUnit tests until they fail, type this into your command line and hit `enter`:

```bash
while (phpunit); do :; done
```

This simple bash script uses a [while loop](http://tldp.org/LDP/Bash-Beginners-Guide/html/sect_09_02.html) to run the command within parentheses over and over again until it receives an error or failure response. You can add any PHPUnit flags you want to the command:

#### Run only one test until it fails
```bash
while (phpunit --filter thatOnePeskyFailingTest); do :; done
```

#### Run one test suite until it fails
```bash
while (phpunit --testsuite suiteName); do :; done
```

Your bash script can be more complicated than one command. You could use a `&&` to chain two commands together. For example, I wanted to delete the contents of my Laravel application's log file and then run the acceptance tests in my app until they failed:
 
```bash
while (> storage/logs/laravel.log && phpunit --testsuite acceptance); do :; done
```

Each time, the script cleared out my logs before running the test, which allowed me to use the logs to debug the issue.

If you ever want to break out of a never-ending test loop, just hold `control` and press `c` (on Mac).

Bash scripting isn't something I choose to do often, but at times it's the best tool for the job. If you are interested in more posts about testing, read my other post on [testing with multiple versions of PHP](https://www.shiphp.com/blog/2018/testing-multiple-versions-of-php) or [follow me on Twitter](https://twitter.com/shiphpnow). 
