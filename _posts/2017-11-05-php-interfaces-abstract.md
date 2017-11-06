---
layout: post
title: "Introduction to Interfaces and Abstract Classes"
author: "Karl Hughes"
categories: [tutorials]
tags: [php,beginner]
---

![](https://i.imgur.com/EDfJTNf.jpg)

Object oriented languages like PHP have many tools for layering objects. Among
PHP’s tools are interfaces and abstract classes which can be used in similar
ways. I think many new developers tend to get confused and misuse them, so I’m
hoping a little tutorial here will help.

### What is an Interface?

An [object interface in
PHP](http://php.net/manual/en/language.oop5.interfaces.php') is like a class
template. It instructs any class which implements it what methods need to be
present and what parameters they should use. In practice, this is can be useful
for guiding developers who want to build a wrapper for your internal system, but
interfaces are also a [common part of dependency injection
libraries](http://php-di.org/doc/best-practices.html).

#### Using an Interface

I won’t cover dependency injection here, but let’s take a look at an interface
in PHP and how we might use it. Note that I’m running PHP 7.1 and [using strict
types](https://stackoverflow.com/a/29967975/977192), but this example will work
without it.

    <?php declare(strict_types=1);
    interface AnimalInterface {
        public function canMakeNoise(): bool;
        public function getNoise($level = "low"): string;
    }
    class Pig implements AnimalInterface {
        public function canMakeNoise(): bool {
            return true;
        }
        public function getNoise($level = "low"): string {
            return $level." oink";
        }
    }
    $pig = new Pig();
    echo $pig->getNoise("medium");

When we run the above script, the terminal will output `medium oink` as the
sound the pig made. This example is trivial because we just have one type of
animal, but if we think about it as a template for any future developers to
create new kinds of animals it starts to gain some usefulness.

For example, what would happen if I tried to create a new animal but didn’t add
the methods required in the `AnimalInterface`?

    <?php declare(strict_types=1);
    interface AnimalInterface {
        public function canMakeNoise(): bool;
        public function getNoise($level = "low"): string;
    }
    class Monkey implements AnimalInterface {
        public function getNoise($level = "low"): string {
            return $level." hoo hoo";
        }
    }
    $monkey = new Monkey();
    echo $monkey->getNoise("high");

PHP will throw a fatal error:

    Fatal error: Class Monkey contains 1 abstract method and must therefore be declared abstract or implement the remaining methods (AnimalInterface::canMakeNoise)

While you don’t have to create an interface for every class you may want to
consider it if you’re going to be passing your code around to other developers
who will build similar classes.

### What are Abstract Classes and Methods?

**Abstract classes** are classes that cannot be instantiated directly, but must
be extended in order to have any practical use.

**Abstract methods** are methods that must be defined in any class that extends
this one. Much like an interface’s methods, PHP will throw an error if an
extending class doesn’t implement an abstract method.

#### Using an Abstract Class

Abstract classes are nice because they can include functionality (fully
implemented methods) and abstract methods. When you want all classes that extend
the abstract class to perform some methods the same, but you also want some
methods to be defined by the child class, you may want to use an abstract class.
Let’s look at an example:

    <?php declare(strict_types=1);
    abstract class BaseAnimal {
        abstract public function canRun(): bool;
        public function isAnimal(): bool {
            return true;
        }
    }
    class Horse extends BaseAnimal {
        public function canRun(): bool {
            return true;
        }
    }
    $horse = new Horse();
    if($horse->canRun() && $horse->isAnimal()) {
        echo "Horses are animals that can run.";
    }

In this case, the command line should show `Horses are animals that can run.`
because the `Horse` class inherits the `isAnimal` method from the `BaseAnimal`
class and it implements its own `canRun` method. This allows us to create
animals in the future which cannot run, but are still seen as animals by anyone
who calls the `isAnimal` method.

### What’s the Difference?

The million dollar question is “What is the difference between an interface and
an abstract class in PHP?” As you can see above, both can be used to instruct
classes to implement certain methods, but they are not wholly interchangeable.
Here’s a good overview from [PHP’s official
documentation](http://php.net/manual/en/language.oop5.abstract.php#82111):

> An Interface is like a protocol. It doesn’t designate the behavior of the
> object; it designates how your code tells that object to act. An interface would
be like the English Language: defining an interface defines how your code
communicates with any object implementing that interface.

> An interface is always an agreement or a promise. When a class says “I implement
> interface Y”, it is saying “I promise to have the same public methods that any
object with interface Y has”.

> On the other hand, an Abstract Class is like a partially built class. It is much
> like a document with blanks to fill in. It might be using English, but that
isn’t as important as the fact that some of the document is already written.

> An abstract class is the foundation for another object. When a class says “I
> extend abstract class Y”, it is saying “I use some methods or properties already
defined in this other class named Y”.

I think it’s helpful to dig into code and see abstract classes and interfaces in
action, so search around on Github. Lots of libraries and frameworks use these
two concepts.
