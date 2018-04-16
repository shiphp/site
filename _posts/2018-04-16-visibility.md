---
layout: post
title: "Class Property and Method Visibility in PHP"
author: "Karl Hughes"
categories: [tutorials]
tags: [php,classes,beginner]
---

Have you ever been confused by the difference between "private" and "protected" properties in PHP? Do you have to declare all your properties as "public"? What would you use a "private" method for?

If you've ever had questions about method or property visibility in PHP, read on. I hope this post will improve your understanding of   

![](https://i.imgur.com/h21Mra9.jpg)

## Classes in PHP

Before jumping into visibility, you [should know a little about PHP classes](https://www.shiphp.com/blog/2017/classes-php). Classes allow you to specify the methods and properties of an object before it's created. Each of these methods and properties (called "members") has what's called "visibility", a setting that determines whether or not other parts of your code can access the member.

## What is the difference between "private", "protected", and "public" keywords?

The difference between the three visibility settings is spelled out in [the PHP Docs](http://php.net/manual/en/language.oop5.visibility.php):

> Class members declared public can be accessed everywhere. Members declared protected can be accessed only within the class itself and by inheriting and parent classes. Members declared as private may only be accessed by the class that defines the member.

But what does this look like in practice? Let's look at an example:

```php
<?php

class Animal
{
    public $sound = false;
    protected $legs = 4;
    private $stomachs = 1;
}

class Cow extends Animal
{
    public $sound = 'Moo';
    private $stomachs = 4;

    public function __construct()
    {
        echo $this->sound . PHP_EOL;
        echo $this->stomachs . PHP_EOL;
    }
}

$bessie = new Cow();

echo $bessie->sound;
echo $bessie->stomachs;

```

When you run the above code in PHP, you will get the output:

```bash
Moo
4
Moo
Fatal error: Uncaught Error: Cannot access private property Cow::$stomachs in classes.php:25
```

This error is telling you that you can't access one of the properties you tried to call. This is because `$stomachs` is private, meaning _it's only available inside the class that contains it_. That's why the first time you called `$this->sound` (inside the `Cow` class) it worked, but the second time caused a fatal error.

Private methods are handled the same way. For example this code:

```php
<?php

class Animal
{
    public $sound = false;
    protected $legs = 4;
    private $stomachs = 1;
}

class Cow extends Animal
{
    public $sound = 'Moo';
    private $stomachs = 4;

    public function __construct()
    {
        echo $this->sound . PHP_EOL;
        echo $this->stomachs . PHP_EOL;
    }
    
    private function getStomachs() {
        return $this->stomachs;
    }
}

$bessie = new Cow();

echo $bessie->sound;
echo $bessie->getStomachs();

```

Will output a fatal error as well. But what if we make the `getStomachs()` method `public`? This code should work:

```php
<?php

class Animal
{
    public $sound = false;
    protected $legs = 4;
    private $stomachs = 1;
}

class Cow extends Animal
{
    public $sound = 'Moo';
    private $stomachs = 4;

    public function __construct()
    {
        echo $this->sound . PHP_EOL;
        echo $this->stomachs . PHP_EOL;
    }

    public function getStomachs() {
        return $this->stomachs;
    }
}

$bessie = new Cow();

echo $bessie->sound . PHP_EOL;
echo $bessie->getStomachs() . PHP_EOL;

```

Giving us the sound and number of stomachs twice with no fatal errors:

```bash
Moo
4
Moo
4
```

This pattern is used in [creating getters and setters](https://stackoverflow.com/questions/12092583/oop-should-all-properties-have-getters-and-setters), which make class properties private and force callers to use public methods to access them. While not necessarily a good pattern, this is pretty common in some languages and frameworks.

### But what about "protected"?

I have glossed over the "protected" visibility because it's a little more complicated. While "private" members cannot be accessed by any code outside the class, "protected" members can be accessed by classes that extend your class. For example, the following code would generate a fatal error:

```php
<?php

class Animal
{
    public $sound = false;
    protected $legs = 4;
}

class Cow extends Animal
{
    public $sound = 'Moo';

    public function __construct()
    {
        echo $this->sound . PHP_EOL;
    }
}

$bessie = new Cow();

echo $bessie->sound. PHP_EOL;
echo $bessie->legs. PHP_EOL;

```

The reason is that the "protected" property `$legs` can only be accessed by the `Cow` class. This code should work:

```php
<?php

class Animal
{
    public $sound = false;
    protected $legs = 4;
}

class Cow extends Animal
{
    public $sound = 'Moo';

    public function __construct()
    {
        echo $this->sound . PHP_EOL;
        echo $this->legs . PHP_EOL;
    }
    
    public function getLegs()
    { 
        return $this->legs;
    }
}

$bessie = new Cow();

echo $bessie->sound. PHP_EOL;
echo $bessie->getLegs(). PHP_EOL;

```

Protected properties are useful when you want to extend a base class, but you may want to overload a method or property value. Be careful because [overloading](http://php.net/manual/en/language.oop5.overloading.php) may be a code smell, so if you find you're always overloading certain methods in a base class, it may be better to implement new methods in the child classes instead.

## When should you use them?

The trickiest part of these visibility keywords is knowing when to use each of them. Because "private" visibility is the safest, it's probably best to initially set class members to private _unless_ you know they'll be used by external callers. Giving limited access to modify properties prevents you (and other developers who use your code) from changing a property somewhere they shouldn't.

While defaulting to "private" may work, you obviously can't get much work done without allowing outside access to your classes. When you know a member will be accessed by child classes, you will have to use "protected" visibility, and when the property or method is intended to be part of the class's public API, its visibility should be "public".

I've found that most classes should have just one or two "public" methods, zero to two "protected" methods, and lots of very small "private" methods. Almost all properties should be "private" or "protected" unless the class is a [data transfer object](https://en.wikipedia.org/wiki/Data_transfer_object). This forces developers to think of each class as doing just one or two related things, and not calling internal private methods without very good reason.

What tips do you have for class member visibility in PHP? [Let me hear your thoughts on Twitter](https://twitter.com/shiphpnow).
