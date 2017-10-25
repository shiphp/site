---
layout: post
title: "Introduction to Classes in PHP"
author: "Karl Hughes"
categories: [tutorials]
tags: [php,beginner]
---

![](https://i.imgur.com/z6eGJzp.jpg)

Like most object-oriented programming languages, classes are the backbone of PHP
projects. Classes allow you to group code into logical pieces, reuse code, and
prevent misuse of functions and variables.

This post will focus on the mechanics of classes and not the underlying concepts
in object oriented software design. If you are interested in the broader
question of “What is a class?” then check out the resources section below. If
you’re ready to learn how to apply object oriented programming to PHP, then read
on!

### Creating a class

First, let’s see what it takes to create a class in PHP. Create a new file
called `ExampleClass.php` and add this code to it:

    <?php
    class ExampleClass {
        
        public $variable;
        
        public function classFunction() {
            return $this->variable;
        }
    }

First, we named the file and class the same thing intentionally. While you don’t
have to follow all the PSR coding standards, PHP has pretty much adopted [PSR-1 for most modern projects](http://www.php-fig.org/psr/psr-1/#namespace-and-class-names), so I’d
strongly recommend it.

Next, we can see that this class has one variable (`public $variable;`) and one
method (`public function classFunction()…`). Both use the visibility `public`
which will be explained in more detail at the end of this post, but for now,
just know that public variables and methods can be used and modified by other
classes.

### Instantiating a class and calling a method

In order to use a class it typically has to be “instantiated” which is a
programming term meaning that we need to make a new object from the class. In
order to instantiate this class, let’s create a new file called `index.php` and
add this code:

    <?php

    // Include the class file
    require('ExampleClass.php');

    // Instantiate the class
    $example = new ExampleClass();

    // Set the class variable
    $example->variable = "Hello World!";

    // Display the results of the class function
    echo $example->classFunction();

Now you can run this [PHP file in your
terminal](https://www.shiphp.com/blog/2017/php-script-in-docker)
with the command: `$ php index.php` . You should see `Hello World!` when it’s
done.

So why would you want to use a class instead of just a simple function?

At first glance, it seems that classes just add more complexity than function
files. If we wanted to write a functional version of the code above in PHP, it
could be very concise:

    <?php

    function globalFunction($variable) {
        return $variable;
    }

    echo globalFunction("Hello World");

But the problem is that as code bases get larger, new developers work with the
code, or we decide to release the code publicly, we need a better way to
organize our code and ensure that developers don’t misuse certain functions.
That’s where [PHP’s class member
visibility](http://php.net/manual/en/language.oop5.visibility.php) helps us out.

### Private member visibility

In order to keep certain elements in a class (called “members”) from being used
by code outside the class, you can mark a method or variable `private` . Here’s
an example modifying our ExampleClass above:

    <?php
    class ExampleClass {
        public $variable;
        private $privateVariable = "I am here.";
        public function classFunction() {
            return $this->variable . " " . $this->privateFunction();
        }
        private function privateFunction() {
            return $this->privateVariable;
        }
    }

Now when we run the same `index.php` file as before we should get the output:
`Hello World! I am here.`

But, if we tried to access the private variable or method directly in the index
file, we’d get a fatal error. For example:

    <?php
    require('ExampleClass.php');
    $example = new ExampleClass();

    // Produces a fatal error in PHP
    $example->privateVariable = "Updating the variable";


### Resources

* [Intro to object oriented programming
[Video]](https://www.youtube.com/watch?v=FKQ6Ohj_PFY)
* [OOP Concepts: Objects and Classes by
Adobe](http://www.adobe.com/devnet/actionscript/learning/oop-concepts/objects-and-classes.html)
