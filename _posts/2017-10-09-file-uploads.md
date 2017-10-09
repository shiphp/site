---
layout: post
title: "File Uploads in PHP"
author: "Karl Hughes"
categories: [tutorials]
tags: [php,intermediate]
---

![](https://i.imgur.com/uOvTo6K.jpg)

A common function for most web applications is uploading and storing files from
users. Doing this securely and reliably can be a challenge, but there are a
couple options if you’re using PHP.

### 1. Use a File Upload Service

If you want to ensure that your file uploads are securely and reliably stored no
matter what web hosting environment you use, you should use a file upload
service rather than storing the files directly on your own server. This method
ensures that your application follows the [Twelve-Factor App
guidelines](https://12factor.net/backing-services), and it allows you to scale
without relying on system admins.

For example, Amazon S3 will allow you to upload files in PHP:

* Set up an Amazon AWS account, [create a bucket in
S3](https://aws.amazon.com/s3/) and [create an API Key and
Secret](http://docs.aws.amazon.com/general/latest/gr/aws-sec-cred-types.html).
* Install the composer package on your local machine: `composer require
aws/aws-sdk-php`.
* Create a PHP file called `upload.php` in the same directory where you installed
the composer package:

```
    <?php require('vendor/autoload.php');

    $config = [
        "bucket" => "...",        // Fill in with your aws bucket name
        "key" => "...",           // Fill in with your AWS Key
        "secret" => "...",        // Fill in with your AWS Secret
        "region" => "us-west-2",  // Fill in with your S3 Bucket's region
        "version" => "2006-03-01",
    ];

    $s3 = new Aws\S3\S3Client($config);

    ?>
    <html>
        <head><meta charset="UTF-8"></head>
        <body>
            <h1>Uploading files to Amazon S3</h1>
    <?php
    if($_SERVER['REQUEST_METHOD'] == 'POST' && isset($_FILES['userfile']) && $_FILES['userfile']['error'] == UPLOAD_ERR_OK && is_uploaded_file($_FILES['userfile']['tmp_name'])) {

    try {
            $upload = $s3->upload($config['bucket'], $_FILES['userfile']['name'], fopen($_FILES['userfile']['tmp_name'], 'rb'), 'public-read');
    ?>
            <p>Upload <a href="<?=htmlspecialchars($upload->get('ObjectURL'))?>">successful</a> :)</p>
    <?php } catch(Exception $e) { ?>
            <p>Upload error :(</p>
    <?php } } ?>
            <h2>Upload a file</h2>
            <form enctype="multipart/form-data" action="<?=$_SERVER['PHP_SELF']?>" method="POST">
                <input name="userfile" type="file"><input type="submit" value="Upload">
            </form>
        </body>
    </html>
```

* Fill in your bucket name, key, secret, and region.
* Run the file in [PHP’s built in
webserver](https://www.shiphp.com/blog/2017/php-website):
`$ php -S localhost:8000 upload.php`
* Go to [http://localhost:8000](http://localhost:8000/) and you should be able to
upload a file and see it in Amazon S3. It’s just that simple!

![](https://i.imgur.com/8GAwr2L.png)

Be sure to use secure environmental variables, perform [file type
validation](https://stackoverflow.com/questions/310714/how-to-check-file-types-of-uploaded-files-in-php),
and set the permissions on your S3 bucket so that only users you want to have
access can upload or view files.

The downside to using a file upload service is that because the files are not
stored on the same server as your web application code, file manipulation and
transfer can take longer. For most apps this is a non-issue (and the problem can
be mitigated by putting your web server in the same data center or caching
commonly used files on your server), but if you think that storing files on your
own server is necessary read on.

### 2. Store the File on Your Server

While easier, this option is typically more prone to errors and security holes.
Giving users the ability to upload files directly to your server means they can
potentially upload malicious scripts or even modify or read your code. Be sure
to [read up on file upload permissions in
PHP](https://stackoverflow.com/questions/10990/what-are-the-proper-permissions-for-an-upload-folder-with-php-apache)
before you use this in live code.

* Create a PHP file called upload.php:

```
    <?php

    if(isset($_POST["submit"])) {
        $target_file = basename($_FILES["fileToUpload"]["name"]);
        if (move_uploaded_file($_FILES["fileToUpload"]["tmp_name"], $target_file)) {
            echo "The file ". basename( $_FILES["fileToUpload"]["name"]). " has been uploaded.";
        } else {
            echo "Something went wrong";
        }
    }

    ?>
    <!DOCTYPE html>
    <html>
    <body>

    <form action="/" method="post" enctype="multipart/form-data">
        Select file to upload:
        <input type="file" name="fileToUpload" id="fileToUpload">
        <input type="submit" value="Upload Image" name="submit">
    </form>

    </body>
    </html>
```

* Run the PHP webserver: `php -S localhost:8000 upload.php`
* Go to [http://localhost:8000/](http://localhost:8000/) and try uploading a file.
You should then see the file right next to your `upload.php` file.
