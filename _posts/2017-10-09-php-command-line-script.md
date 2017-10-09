---
layout: post
title: "Writing a PHP Command Line Script"
author: "Karl Hughes"
categories: [tutorials]
tags: [php,command line,beginner]
---

![](https://i.imgur.com/UOy71Kg.jpg)

To write a PHP command line script, you will need [PHP
installed](http://php.net/manual/en/install.php) on your Mac, Windows, or Linux
machine, a terminal command line tool like
[Bash](https://en.wikipedia.org/wiki/Bash_(Unix_shell)), and a text editor like
[Sublime](https://www.sublimetext.com/3).

1.  To write a command line script in PHP, open up a new blank file your favorite
text editor.
1.  Add an opening `<?php` tag to the very beginning of the new file.
1.  On the next line, save some text to a PHP variable: `$text = "Hello World!";`
1.  Add a new line below that will output the text: `echo $text;`
1.  Now, save the file as `hello.php`. Be sure you know where the file is saved so
that you can find it in your command line terminal.
1.  Open your command line terminal and navigate to the PHP file you just created.
1.  Run the file from your terminal by typing `php hello.php` and then hitting the
`enter` or `return` key.

If you did everything correctly, you should see `Hello World!` in the command
line of your terminal.

### References

#### 1. hello.php

    <?php

    $text = "Hello World!";

    echo $text;
