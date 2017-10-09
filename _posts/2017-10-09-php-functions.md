---
layout: post
title: "Writing Functions in PHP"
author: "Karl Hughes"
categories: [tutorials]
tags: [php,beginner]
---

![](https://i.imgur.com/5BLA8qE.jpg)

Functions are one of the basic building blocks of any programming language. They
allow you to define blocks of code that can be called many times in many places
throughout your program. Functions are great for reusability, but they also help
make code more concise and easier to read. Let’s look at some examples.

#### Using built-in PHP functions

PHP comes with [many global
functions](http://php.net/manual/en/functions.internal.php), and many more can
be added using extensions. These functions are well-documented in the [PHP
manual](http://php.net/manual/en/), but you will probably use some so often that
you won’t need to look them up. Calling a built-in function is as simple as
writing the name of the function and including any parameters in parentheses.

    <?php

    $string = "hello world!";

    // Converts a string to all uppercase letters
    $result = strtoupper($string);

    echo $result;
    // Output: "HELLO WORLD!"

Notice that you didn’t need to write any code to go through each letter and
convert it to uppercase; PHP did everything for you.

#### Writing a global function

All functions defined outside of a class or namespace are “global” in PHP,
meaning that you can use them anywhere in your program. To write a new function,
you just have to define it somewhere in your PHP code:

    // Custom function that reverses the order of words

    function flip_words($string) {
      $unflipped = explode(' ', $string);
      $flipped = [];
      foreach($unflipped as $key => $word) {
        $flipped[(count($unflipped) - $key)] = $word;
      }
      ksort($flipped);
      return implode(' ', $flipped);
    }

#### Calling a function

In order to call the function we just wrote, we do the same thing we did for
calling a built-in PHP function:

    $result = flip_words("hello world, I am here!");
    echo $result;
    // Output: "here! am I world, hello"

Even though PHP runs synchronously (meaning it processes code one line after
another in the order it’s written), the function can be placed above or below
the lines calling it. This can be helpful if you want to organize your code by
putting all the functions at the bottom of your file instead of at the top.

#### Special kinds of functions

While the basics of functions are pretty straightforward, functions can do a lot
more and are expanded upon in interesting ways as you dig into PHP. If you’re
interested in learning more, here are some links to the PHP manual:

* [Class functions](http://php.net/manual/en/language.oop5.php)
* [Static functions](http://php.net/manual/en/language.oop5.static.php)
* [Closures/anonymous function](http://php.net/manual/en/functions.anonymous.php)
* [Callbacks](http://php.net/manual/en/language.types.callable.php)
* [Passing input and output by
reference](http://php.net/manual/en/language.references.pass.php)
* [Namespaced functions](http://php.net/manual/en/language.namespaces.basics.php)
