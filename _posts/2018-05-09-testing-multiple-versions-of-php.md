---
layout: post
title: "Testing Your Code with Multiple Versions of PHP Using Docker"
author: "Karl Hughes"
categories: [tutorials]
tags: [php,testing,intermediate]
---

About a year ago, I spent some time working with an open source project called [PHP Crud API](https://github.com/mevdschee/php-crud-api). The project creates a RESTful API from a relational database using a single PHP script. It's quite an impressive feat of engineering, but as I started working on the project, I realized I needed a reliable way to test my changes in different versions of PHP. That's where Docker comes in.

One of the biggest perks to using Docker is the ability to quickly switch between different versions of PHP. In order to demonstrate this feature, I've set up an example repository [here](https://github.com/karllhughes/docker-testing-examples/tree/master/ex-1), but let's walk through how we might test a function in PHP 5.6 and PHP 7.2 with Docker.

![](https://i.imgur.com/U6fSWpz.jpg)

## The Code

Create a file called `index.php` and add a function called `sortArrayOfObjects`:

```php
<?php

/**
 * Function to be tested
 *
 * Includes a number of features that require PHP 7.0+, including:
 * - Scalar type hints
 * - Return types
 * - The "spaceship" operator
 *
 */
function sortArrayOfObjects(string $field, array $objects): array
{
    usort($objects, function ($a, $b) use ($field) {
        return $a->{$field} <=> $b->{$field};
    });
    return $objects;
}
```

This function will take an [array](https://www.shiphp.com/blog/2017/php-arrays) of objects and sort them by a field you choose. It uses several features only available in PHP 7.0 including [scalar type hints](https://www.shiphp.com/blog/2017/type-hinting-return-types), [return types](https://www.shiphp.com/blog/2017/type-hinting-return-types), and [the "spaceship" operator](https://wiki.php.net/rfc/combined-comparison-operator). 

## The Test

Now, in order to test it, just add the following code to the bottom of the `index.php` file:

```php
<?php

/**
 * PHPUnit test code
 *
 * Normally this would be in another file, but we're keeping it simple.
 * This test simply verifies that the sorting works, but will throw
 * a syntax error in PHP < 7.0.
 */
use PHPUnit\Framework\TestCase;

class SortArrayOfObjectsTest extends TestCase
{
    function testItSortsWhenFieldExists()
    {
        $objects = [
            (object) [
                'sort_order' => '2',
                'name' => 'The first shall be last',
            ],
            (object) [
                'sort_order' => '1',
                'name' => 'The last shall be first',
            ],
        ];
        $result = sortArrayOfObjects('sort_order', $objects);
        $this->assertEquals($objects[0], $result[1]);
        $this->assertEquals($objects[1], $result[0]);
    }
}
```

## Installing Dependencies

Next, add a `composer.json` file to the root of your project:

```json
{
    "require-dev": {
        "phpunit/phpunit": "^5"
    }
}
``` 

And install the dependencies using [Docker and Composer](https://www.shiphp.com/blog/2017/composer-php-docker):

```bash
docker run --rm -v $(pwd):/app -w /app composer install
```

## Running the Test

Now you can run your test file in PHP 7.2 to confirm that it works:

```bash
docker run --rm -v $(pwd):/app -w /app php:7.2 vendor/bin/phpunit index.php

> PHPUnit 5.7.27 by Sebastian Bergmann and contributors.
> ...
> OK (1 test, 2 assertions)
```

Or you can run it in PHP 5.6 to confirm that older versions of PHP won't work:

```bash
docker run --rm -v $(pwd):/app -w /app php:5.6 vendor/bin/phpunit index.php

> Parse error: syntax error, unexpected ':', expecting '{' in /app/index.php on line 12
```

Using this method, you can quickly test any application or library in different versions of PHP without installing them locally or setting up a virtual machine.

What tips do you have for testing PHP applications with Docker? [Let me hear your thoughts on Twitter](https://twitter.com/shiphpnow).
