---
layout: post
title: "Introduction to Writing Unit Tests in PHP with PHPUnit and Docker"
author: "Karl Hughes"
categories: [tutorials]
tags: [php,docker,intermediate,phpunit]
---

![](https://i.imgur.com/RnnJprN.png)

Testing your code is a great way to improve quality and minimize [bugs due to
regression](https://en.wikipedia.org/wiki/Software_regression). PHP has a couple
frameworks available for unit testing, but the most popular by far is
[PHPUnit](https://phpunit.de/). Because unit tests are going to be run on a
variety of environments, running tests within Docker containers can make tests
faster and more portable. This tutorial will walk you through setting up a PHP
application with unit tests that can be run in Docker.

### 1. Setting up a PHP Class to Test

First let’s create a simple PHP class in a file called `ExampleClass.php`:

    <?php
    class ExampleClass {
        function addOne(int $number): int {
            return $number + 1;
        }
    }

This class has one function called `addOne` that will simply add `1` to an
integer when passed in.

### 2. Installing PHPUnit

Next, let’s install and configure PHPUnit to test this class. Because we’re
using Docker, we’ll install PHPUnit via [Composer in a
container](https://medium.com/shiphp/installing-php-packages-with-docker-and-composer-1fb907637863):

    docker run --rm -v $(pwd):/app composer/composer:latest require --dev phpunit/phpunit ^6.0

This will install version 6.0 of PHPUnit into our directory’s new `vendor`
folder.

### 3. Writing a Unit Test

I won’t go into detail on [how to write good unit
tests](https://stackoverflow.com/questions/3258733/new-to-unit-testing-how-to-write-great-tests),
but let’s look at a simple test we might do based on the simple class above:

    <?php
    use PHPUnit\Framework\TestCase;
    class ExampleTest extends TestCase
    {
        public function testItCanAddOneToInteger(): void
        {
            $exampleClass = new ExampleClass();
            $input = rand(1, 100);
            $result = $exampleClass->addOne($input);
            $this->assertEquals($input + 1, $result);
        }
    }

This test is stored in a file called `ExampleTest.php` in the same directory as
the class we’re testing. Typically, you’ll want to put your application files in
a different directory from your tests, but since this is a simple example,
everything is in the root directory.

In the function `testItCanAddOneToInteger`, we instantiate the `ExampleClass`,
get a random number, call the `addOne` method, and then assert that the result
is correct.

### 4. Running Unit Tests

Finally, to run a unit test you need to make PHPUnit aware of your code files
and test files. The best way to do that in a large application is using
[PHPUnit’s XML configuration
file](https://phpunit.de/manual/current/en/organizing-tests.html#organizing-tests.xml-configuration),
but since this is a very simple example, we’ll just use the command line
interface.

    docker run -v $(pwd):/app --rm phpunit/phpunit:latest --bootstrap ExampleClass.php ExampleTest.php

This docker command runs the latest version of PHPUnit and uses the
`--bootstrap` flag to tell it which files it needs. If everything was done
correctly, you should see something like this in your terminal output:

    PHPUnit 6.0.13 by Sebastian Bergmann, Julien Breux (Docker) and contributors.

    .                                                                   1 / 1 (100%)

    Time: 77 ms, Memory: 2.00MB

    OK (1 test, 1 assertion)

This indicates that one unit test was run and that it passed.
