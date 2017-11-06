---
layout: post
title: "Switch Statements in PHP"
author: "Karl Hughes"
categories: [tutorials]
tags: [php,beginner]
---

![](https://i.imgur.com/w9R6Goe.jpg)

While the `switch` statement isn’t as popular as [conditional
operators](https://medium.com/shiphp/conditional-operators-in-php-8f528aec4c82)
like `if` and `elseif` in PHP, it can still be useful when evaluating a number
of possible outcomes. Using a `switch` statement is pretty simple, so let’s look
at an example:

    <?php
    $var = rand(0,2);
    switch($var) {
        case 1:
            echo "The magic number is one.";
            break;
        default:
            echo "The magic number is unknown.";
    }

This script will output either `The magic number is unknown.` or `The magic
number is one.` depending on what random number is generated.

* The `switch($var)` line determines the variable to be evaluated.
* The `case 1:` block will be run if the variable being evaluated equals `1`. Note
that this uses [loose
comparisons](http://php.net/manual/en/types.comparisons.php#types.comparisions-loose),
meaning that `1` or `"1"` will evaluate to true in this case.
* `break;` is a stopping point for each case’s block. This ensures that the next
block (either another `case` or `default` block) will not run.
* Finally `default:` will be evaluated if none of the `case` statements was true.

### A More Advanced Example

You can extend the logic from our first example to more complex statements and
cases.

    <?php
    $var = rand(0,5);
    switch($var) {
        case 1:
            echo "The magic number is one.";
            break;
        case 2:
            echo "The magic number is two.";
            break;
        case $var > 2:
            echo "The magic number is too big!";
        default:
            echo "The magic number is unknown.";
    }

This time, we’re evaluating a random number between `0` and `5` inclusive. The
first two `case` statements work the same as above, but the third one is a
little different. It evaluates whether the number is greater than `2` and if so
it says the number is `too big!`. You may also notice that there’s no `break;`
statement, meaning that if `case $var > 2` is reached it will continue on to the
`default:` statement as well. So your output might look like any one of the
following:

* If `$var == 1`: `The magic number is one.`
* If `$var == 2`: `The magic number is two.`
* If `$var == 3,4,5`: `The magic number is too big! The magic number is unknown.`
* If `$var == 0`: `The magic number is unknown.`

As you can see, `switch` statements can be used to perform logical operations
that would be cumbersome using only embedded `if` statements. They also tend to
be more readable when you have lots of potential scenarios.
