---
layout: post
title: "Array Functions in PHP"
author: "Karl Hughes"
categories: [tutorials]
tags: [php,beginner,arrays]
---

![](https://i.imgur.com/8rMuRoT.jpg)

Arrays are an important and widely used type in PHP. They are essentially
ordered maps that allow developers quick access to key-value storage in their
applications. For some basic array examples, [see my previous post of working
with arrays in PHP](https://www.shiphp.com/blog/2017/php-arrays).

PHP has [over 75 array functions](http://php.net/manual/en/ref.array.php) to
help you manipulate and use arrays more efficiently. Since that’s a bit
overwhelming, I’m just going to go over a few of the more common ones.

### 1. array_diff

If you have two arrays and you want to find values that are in the first, but
not the second, `array_diff` is probably your best bet. It will compare two or
more arrays and return all the values in the first array that are not in the
other arrays.

#### Code

    $array1 = ['red', 'blue', 'green'];
    $array2 = ['red', 'brown', 'green'];
    print_r(array_diff($array1, $array2));

#### Output

    Array
    (
        [1] => blue
    )

*Note: You can similarly compare array keys with `array_diff_key`.*

### 2. array_filter

When you want to remove certain values from an array `array_filter` is usually a
good option. It allows you to pass a callback function that filters items from
an array.

#### Code

    $array = [1, 100, 102, 103];
    // Filters out array values < 100
    $filteredArray = array_filter($array, function ($value) {
        return $value >= 100;
    });
    print_r($filteredArray);

#### Output

    Array
    (
        [1] => 100
        [2] => 102
        [3] => 103
    )

*Note: You can also filter using keys with `array_filter`. Be sure to check
out the official documentation for [more detailed
examples](http://php.net/manual/en/function.array-filter.php).*

### 3. array_intersect

If you need to know which values are contained in all of two or more arrays you
can use `array_intersect`.

#### Code

    $array1 = ['red', 'blue', 'green'];
    $array2 = ['red', 'brown', 'green'];
    print_r(array_intersect($array1, $array2));

#### Output

    Array
    (
        [0] => red
        [2] => green
    )

*Note: `array_intersect` ignores the keys in your compared arrays, but
`array_intersect_assoc` does not.*

### 4. array_keys

Sometimes you just need keys from your array, and `array_keys` can be very
useful for this. It also provides an optional second parameter to search the
array values and only return keys of matched elements.

#### Code

    $array = ['a' => 'red', 'b' => 'blue', 'c' => 'green'];
    print_r(array_keys($array));
    print_r(array_keys($array, 'blue'));

#### Output

    Array
    (
        [0] => a
        [1] => b
        [2] => c
    )
    Array
    (
        [0] => b
    )

*Note: by default the `search_value` parameter uses a loose comparison, but
you can include a [third
parameter](http://php.net/manual/en/function.array-keys.php) to enforce strict
matches.*

### 5. array_map

This is one of my favorite array functions. It allows you to loop over an array
and manipulate each value or call some external function. It can be used as [a
more concise looping
method](https://www.shiphp.com/blog/2017/php-loops).

#### Code

    $array = ['a' => 'red', 'b' => 'blue', 'c' => 'green'];

    $array = array_map(function ($value) {
        return $value . " modified";
    }, $array);

    print_r($array);

#### Output

    Array
    (
        [a] => red modified
        [b] => blue modified
        [c] => green modified
    )

*Note: You can pass multiple arrays into `array_map` and use each array as a
value in the callback function. For more detailed examples [check the
documentation](http://php.net/manual/en/function.array-map.php).*

### 6. array_merge

If you have two arrays and would like to merge them into a single array,
`array_merge` makes it a snap.

#### Code

    $array1 = ['red', 'blue', 'green'];
    $array2 = ['red', 'brown', 'green'];
    print_r(array_merge($array1, $array2));

#### Output

    Array
    (
        [0] => red
        [1] => blue
        [2] => green
        [3] => red
        [4] => brown
        [5] => green
    )

*Note: When using non-numeric array keys `array_merge` works a little
differently. It will actually overwrite values with identical keys. This can be
good, but don’t let it catch you off guard.*

### 7. array_pop

`array_pop` actually has a couple of use cases. You can use it to shorten an
array by one and you can use it to retrieve the last element in an array. Be
careful though because it modifies the array either way.

#### Code

    $array = ['red', 'blue', 'green'];

    print_r(array_pop($array));
    print_r($array);

#### Output

    green

    Array
    (
        [0] => red
        [1] => blue
    )

### 8. array_search

When you want to find a specific value within an array or you want to check
whether the value exists, `array_search` can be very useful. It returns the key
corresponding to the found value.

#### Code

    $array = ['red', 'blue', 'green'];
    print_r(array_search('blue', $array));

#### Output

    1

*Note: If an item is not found, `array_search` will return `false` but if
the item is found at index `0` then it will return `0`. This is one of those
cases where strict type checking becomes important.*

### 9. array_reduce

When you want to convert an array into a single value or string, add up all the
values in an array, or something like that, `array_reduce` can come in handy.

#### Code

    $array = ['red', 'blue', 'green'];
    print_r(array_reduce($array, function ($carry, $item) {
        return $carry . " " . $item;
    }));

#### Output

    red blue green

*Note: If you just want to convert an array into a string with separators as in
the above example, you’re probably better off using `implode`, but
`array_reduce` is still good for more complex conversions.*

### 10. array_slice

I find myself frequently trying to extract the beginning or end of an array, so
`array_slice` has come in handy a number of times. It allows you to take a cut
of an array by specifying an offset and length.

#### Code

    $array = ['red', 'blue', 'green'];
    // First element in an array
    print_r(array_slice($array, 0, 1)) . 
    ;
    // Last element in an array
    print_r(array_slice($array, -1, 1)) . 
    ;
    // Second two elements
    print_r(array_slice($array, 1, 2)) . 
    ;

#### Output

    Array
    (
        [0] => red
    )
    Array
    (
        [0] => green
    )
    Array
    (
        [0] => blue
        [1] => green
    )

*Note: `array_slice` will reset your keys by default, but it has a fourth
parameter that you can [set to retain keys](http://php.net/manual/en/function.array-slice.php).*

### 11. array_values

Similar to `array_keys` there is a function to get just the values from an array
and reset the indices to their numerical defaults. `array_values` allows you to
pass in an array and get only the values of each item back, which can be useful
if you’ve put your array into some custom order and want the indices to reflect
that.

#### Code

    $array = ['a' => 'red', 'b' => 'blue', 'c' => 'green'];
    print_r(array_values($array));

#### Output

    Array
    (
        [0] => red
        [1] => blue
        [2] => green
    )

### 12. count

Another common task when dealing with arrays is determining the length of the
array. `count` can actually be used for many different variable types, but I
find it most useful for counting the number of elements in an array.

#### Code

    $array = ['red', 'blue', 'green'];

    print_r(count($array));

#### Output

    3

*Note: You can also set count to `COUNT_RECURSIVE` which means it will count
the number of sub-elements within an array of arrays. Check out [the
documentation](http://php.net/manual/en/function.count.php) for details.*

### 13. in_array

Rather than looping over an array to check whether or not a value appears in the
array, you can simply call `in_array` and let PHP do the work. This function
will return a boolean `true` if it finds the value and `false` if it does not.

#### Code

    $array = ['red', 'blue', 'green'];
    print_r(in_array('blue', $array));

#### Output

    1

### 14. sort/ksort/usort

There are a number of built-in sorting functions for arrays in PHP, but I’ll
limit myself to three of the most common. First, `sort` will compare each value
in your array and order them from lowest to highest. Second, `ksort` will
compare each *key* in your array and order them from lowest to highest. Finally,
`usort` lets you define a function that will compare each value and do the
sorting.

#### Code

    // sort
    $array = [10 => 90, 9 => 93, 7 => 92, 13 => 91];

    sort($array);

    print_r($array);

    // ksort
    $array = [10 => 90, 9 => 93, 7 => 92, 13 => 91];

    ksort($array);

    print_r($array);

    // usort
    $array = [10 => 90, 9 => 93, 7 => 92, 13 => 91];

    usort($array, function ($a, $b) {
        // Sorts by reverse numerical order
        return $a < $b;
    });

    print_r($array);

#### Output

    Array
    (
        [0] => 90
        [1] => 91
        [2] => 92
        [3] => 93
    )
    Array
    (
        [7] => 92
        [9] => 93
        [10] => 90
        [13] => 91
    )
    Array
    (
        [0] => 93
        [1] => 92
        [2] => 91
        [3] => 90
    )

*Note: In each of these functions the array is [passed by
reference](http://php.net/manual/en/language.references.pass.php), meaning the
function modifies the array directly and doesn’t return the modified array. Keep
this in mind if you want to keep a copy of your original array.*

If you’d like to see more array functions or learn more about arrays in PHP, be
sure to [check out the manual](http://php.net/manual/en/ref.array.php).
