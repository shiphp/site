---
layout: post
title: "Try/Catch Blocks in PHP"
author: "Karl Hughes"
categories: [tutorials]
tags: [php,beginner]
---

![](https://i.imgur.com/spkkZLS.jpg)

When things go wrong within your program it’s a good idea to handle the failure
gracefully in some way. One of the best ways to [deal with
exceptions](http://php.net/manual/en/language.exceptions.php) is by using a
`try...catch` block. Let’s take a look at how this works.

### The Basics

First let’s look at the success case — in other words when an exception is not
thrown:

    <?php
    try {
        echo "Hello World!";
    } catch (Exception $e) {
        echo "Whoops, something went wrong!";
    }

The above script will output `Hello World!` and everything within the `catch`
block will be ignored. But, things get more interesting when your code within
the `try` block throws an exception. For example:

    <?php
    try {
        echo "Hello World!";
        throw new Exception("Something went wrong");
    } catch (Exception $e) {
        echo $e->getMessage();
    }

In this case, the command line will output `Hello World!Something went wrong`.
It executed the good code, but when an exception was thrown it went right to the
`catch` block and echo’ed the message.

The good news is that it didn’t throw an error, so this won’t reveal anything
about the internals of our code to users unless we want to include that in the
message.

### Advanced Exception Handling

Beyond the basics of `try/catch` — which are undoubtedly useful — there are some
advanced features that you can take advantage of in PHP.

#### Multiple Exception Types

What if we want to handle different kinds of exceptions differently? Not a
problem, just type-hint the exception class.

    <?php
    try {
        throw new OutOfRangeException();
    } catch (OutOfBoundsException $e) {
        echo "You are out of bounds!";
    } catch (OutOfRangeException $e) {
        echo "You are out of range!";
    }

In the above example, we will see `You are out of range!`

#### “Finally” Blocks

What if you want to execute some code after the `try/catch` block, even if the
exception was uncaught? That’s where `finally` comes in:

    <?php
    try {
        throw new Exception();
    } catch (OutOfBoundsException $e) {
        echo "You are out of bounds!";
    } catch (OutOfRangeException $e) {
        echo "You are out of range!";
    } finally {
        echo "Nothing left";
    }

Within the `try` block we throw an exception that neither catch block can
handle, but before the fatal error is shown, the `finally` block is executed, so
our command line output looks something like this:

    Nothing left
    PHP Fatal error:  Uncaught Exception in index.php:4
    ...

### Catching Exceptions in Practice

Showing fatal errors to end users of our web app or command line script is
usually a bad idea, so use `try/catch` blocks whenever possible, but balance
their use with the fact that internally developers may need meaningful output
when they encounter errors. One strategy is to log any caught failures and show
users a less descriptive error message. For example:

    try {
        throw new Exception();
    } catch (Exception $e) {
        echo "Something went wrong and our devs have been notified";
        Log::info($e->getMessage());
    }

Just be sure that you import some kind of [logging
library](https://stackoverflow.com/questions/8270375/php-good-log-library/8270504)
as`Log`.
