---
layout: post
title: "Type Hinting and Return Types in PHP 7"
author: "Karl Hughes"
categories: [tutorials]
tags: [tutorials,php,php7,intermediate]
---

![](https://i.imgur.com/IVWoITH.jpg?1)

Many developers prefer strongly-typed languages, and critics have often [dogged
PHP](http://david.heinemeierhansson.com/arc/000074.html) because its weak typing
opens users up to errors and vulnerabilities.

While PHP is still a “weakly-typed” language (meaning variables can change types
throughout the program’s lifecycle), PHP 7.0 sought to give developers more
control over how they specify function inputs and outputs in two ways: type
hinting and return types.

### Type Hinting in PHP 7

[Type
hinting](http://php.net/manual/en/functions.arguments.php#functions.arguments.type-declaration)
is when a function input’s type is specified in the definition of the function’s
parameters. That sounds like a mouthful, but it’s really not that hard to grasp
in practice once you are familiar with [functions in
PHP](http://www.shiphp.com/blog/2017/php-functions).

Let’s look at a simple function that converts a string to an integer in PHP:

    function stringToInteger(string $number) {
      return (int) $number;
    }

    $result = stringToInteger("12");

    var_dump($result);

When run, this file will output  indicating that our function worked, but what
happens when we try to pass in a variable that is not a string? For example,
what if we passed in an array like ?

We would get an error like this:

    PHP Fatal error:  Uncaught TypeError: Argument 1 passed to stringToInteger() must be of the type string, array given, called in returns.php on line 7 and defined in returns.php:3

That helpful error is telling us that our input for this function should be a
string. If you’re using an IDE like
[PHPStorm](https://www.jetbrains.com/phpstorm/), you will even see an error
before you run the program, and that could save you serious time and effort
debugging.

But because PHP is a weakly typed language and because it [tries to convert
types on its own](http://php.net/manual/en/language.types.type-juggling.php),
running the function with an integer (or even a boolean) will not throw an
error:

    var_dump(stringToInteger(12));
    // Output: int(12)

    var_dump(stringToInteger(true));
    // Output: int(1)

This is where PHP gets tricky, and it’s easy to see why critics will say that
this is a weakness of the language. Sometimes it’s just too forgiving!

### Return Types in PHP 7

Another useful feature for people who like strong typing is [PHP’s return type
declarations](http://php.net/manual/en/functions.returning-values.php#functions.returning-values.type-declaration).
While type hinting allows you to specify a function’s input variable types,
return typing allow you to specify a function’s output variable type. Let’s look
at our string to integer converter:

```
function stringToInteger(string $number): int {
  return (int) $number;
}

$result = stringToInteger("12");
```

We’ve added  to the function declaration line just before the opening bracket.
This will tell other developers and our IDE to expect an integer as a return
type from this function. The actual output of the function doesn’t change, but
what if we remove the  designation from the function? For example:

```
function stringToInteger(string $number): int {
  return $number;
}
```

This function will still return  but the return type designation is actually
doing the re-casting of our input variable. This is pretty cool because now
we’ve got PHP’s type juggling doing work for us.

This could also be useful in a callback because it can allow you to write more
concise code:

```
// Return types in callback
$array = ["12", "1.3", "3"];

$result = array_map(function ($n): int { return $n; }, $array);

var_dump($result);

/* Output:
array(3) {
  [0] =>
  int(12)
  [1] =>
  int(1)
  [2] =>
  int(3)
}
*/
```

You might be replacing readability for brevity in the above example, but that’s
a tradeoff that you’ll have to decide to make or not. Either way, it’s good to
know what’s possible with return types and type hinting in PHP.

### References

#### returns.php

```
<?php

// Type hinting and return types in function

function stringToInteger(string $number): int {
  return $number;
}

$result = stringToInteger("12");

var_dump($result);

// Type hinting and return types in callback

$array = ["12", "1.3", "3"];

$result = array_map(function (string $n): int { return $n; }, $array);

var_dump($result);
```
