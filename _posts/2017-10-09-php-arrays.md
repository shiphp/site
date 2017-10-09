---
layout: post
title: "Working with Arrays in PHP"
author: "Karl Hughes"
categories: [tutorials]
tags: [php,arrays,beginner]
---

![](https://i.imgur.com/UMsmk9f.jpg)

Arrays are widely used in PHP to store groups of data when an object or string
will not suffice. Those familiar with other languages may equate [PHP’s
arrays](http://php.net/manual/en/language.types.array.php) to dictionaries,
lists, collections, or stacks, but essentially a PHP array is a reliable
key-value storage system in the language. Let’s look at what an array can do in
practice.

#### Creating an array

There are several ways to create an array in PHP. You can use the `array()`
function:

    $array = array(
      'value1',
      'value2',
      'value3',
    );

Or you can use the more succinct syntax if you’re working in PHP 5.4+:

    $array = [
      'value1',
      'value2',
      'value3',
    ];

The two arrays above are actually creating keys behind the scenes. These keys
will start at `0` and increment by one as each new element is added to the
array. You can explicitly specify the key for each value if you’d like:

    $array = [
      1 => 'value1',
      0 => 'value2',
      2 => 'value3',
    ];

Or you can use strings as keys for the array:

    $array = [
      'key1' => 'value1',
      'key2' => 'value2',
      'key3' => 'value3',
    ];

Once you have data stored in an array, there’s a lot you can do with it, so
let’s continue on.

#### Debugging arrays

You might want to debug an array. If you’re [running a PHP command line
script](https://medium.com/shiphp/writing-a-php-command-line-script-31073babdef),
then you can create a file like this:

    <?php

    $array = [
      'value1',
      'value2',
      'value3',
    ];

    print_r($array);

When you run it from the terminal command line, you should see something like
this:

    Array
    (
        [0] => value1
        [1] => value2
        [2] => value3
    )

You can also print one single value of an array:

    $array = [
      'value1',
      'value2',
      'value3',
    ];

    print_r($array[0]);

Or you can use `var_dump()` to output your array with more details:

    $array = [
      'value1',
      'value2',
      'value3',
    ];

    var_dump($array);

Which will generate output like this:

    /path/to/file/array.php:7:
    array(3) {
      [0] =>
      string(6) "value1"
      [1] =>
      string(6) "value2"
      [2] =>
      string(6) "value3"
    }

Now we can see what our array contains, which is helpful as we start to
dynamically modify the array.

#### Modifying arrays

Once you’ve created an array, you may find that you need to update individual
values or overwrite the whole thing. You can overwrite an entire array in the
same way it was initially created:

    $array = [
      'value1',
      'value2',
    ];
    $array = [
      'value3',
      'value4',
    ];

The first value of `$array` will be overwritten and the second value will be all
that remains.

In the real world, it’s probably more useful to add, remove, and update
individual values within an array. You can “push” a single value onto the end of
an array like so:

    $array[] = 'value3';

Or you can overwrite a single value in an existing array by specifying the key:

    $array[0] = 'value4';

Finally, you can completely delete a single key-value pair from an array:

    unset($array[0]);

#### Looping over arrays

There are many ways to loop over each key and value in an array, but the
simplest and most widely useful is probably a `foreach` loop:

    foreach($array as $key => $value) {
      echo "key: ".$key.PHP_EOL;
      echo "value: ".$value.PHP_EOL;
    }

This code will output something like:

    key: 0
    value: value1
    key: 1
    value: value2
    key: 2
    value: value3

PHP also includes over 60 [array
functions](http://php.net/manual/en/ref.array.php) that can help you loop over
and modify arrays in more creative ways, but I’ll save those for another
article.
