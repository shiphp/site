---
layout: post
title: "Conditional Operators in PHP"
author: "Karl Hughes"
categories: [tutorials]
tags: [php,beginner]
---

![](https://i.imgur.com/uX7KDjy.jpg)

Like any programming language, PHP supports conditional statements to execute
specific blocks of code if a statement is true (or false) or some condition is
met. If you’re familiar with conditional statements in other programming
languages PHP should give you no trouble, but there are a couple lesser-known
operators (ternary and null coalescing) that will be covered at the end.

### “if” and “else”

Let’s look at a simple `if` statement first:

    <?php
    $variable = true;
    if ($variable) {
        echo "Result is true";
    } else {
        echo "Result is false";
    }

Upon execution, this script will output `Result is true` because the variable
was set to true, and if we set it to false (or [any value evaluated as
false](http://php.net/manual/en/types.comparisons.php)) then the script will
output `Result is false`. This simple example shows you how programs can branch
using conditional statements.

### “elseif”

But what if we have three or more possible values for our variable? We may want
to test a number of conditions and take a different path for each. You can do
this by nesting `if — else` blocks, but that gets messy really quick. The better
solution is to use an `elseif` statement:

    <?php
    $number = rand(1, 3);
    if ($number == 3) {
        echo "The number is three";
    } elseif ($number == 2) {
        echo "The number is two";
    } else {
        echo "The number must be one";
    }

In this example, if the random integer generated is equal to `3` it will get
caught in the first block, if equal to `2` it will execute the second, and if
neither of the first two conditionals are met, the `else` block will be
triggered (and we assume the number is `1`).

### Ternary Operators

`if-else` blocks tend to take up a lot of space, so PHP also has [support for
ternary
conditionals](https://davidwalsh.name/php-shorthand-if-else-ternary-operators),
which can be put onto a single line. This can greatly cut down on the amount of
space taken by your code, and make things faster to read. For example, we can
output one of two choices by using just one line of code:

    <?php
    echo rand(1, 2) == 1 ? "One is the answer" : "The answer must be two";

Let’s break this down piece by piece:

* `echo` will output the result of this statement to the command line.
* `rand(1, 2)` will generate a random number with a value of either 1 or 2.
* `== 1` tests whether the number generated is equal to `1`. If so, this part will
evaluate to `true`.
* `?` is the ternary test. It’s shorthand for `if(...) {` but the condition comes
*before* the question mark.
* `"One is the answer"` will be returned if the conditional statement is true.
* `: "The answer must be two"` will be returned if the conditional statement is
not true. The `:` is essentially equivalent to `} else {` in the previous
examples.

So the above script will write `One is the answer` to the command line if the
random number is equal to `1` and `The answer must be two` if it’s not.

### Ternary Shorthand and Null Coalescing

Finally, PHP has two ways to further shorten ternaries, but one (null
coalescing) is only available in PHP 7+. If we just want to output the number
generated by a random number generator, then we could shorten our code like so:

    <?php

    $number = rand(0, 2);
    echo $number ?: "None";

If the number is `1` or `2` then this script will echo the number, but if it’s
`0` — which evaluates to false — then it will echo `None`. This can be useful if
you’re not sure if a previous value has been updated or not, but it fails if a
previous value is completely unset:

    <?php echo $number ?: "None";

The above script will throw an error because `$number` is not defined, so we can
instead use the [null coalesce
operator](https://lornajane.net/posts/2015/new-in-php-7-null-coalesce-operator)
to first test if it’s set:

    <?php echo $number ?? "None";

This script will output `None` but will not throw an error assuming you’re using
PHP 7+.
