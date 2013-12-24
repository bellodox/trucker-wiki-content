This guide will walk you through deploying a simple php application on Trucker. It is a supplement to the [[deploy your application]] section of the documentation.

Trucker does not automatically detect and use a buildpack for PHP applications, so you will just need to specify which buildpack to use during the application deployment. 

## Prerequisites

You should have a Trucker account, the trucker command line client installed on your machine, and logged in the Trucker service. If not, have a look at our [[getting started]] section.

## Example PHP application
We are going to create a simple PHP application that provides us with the phpinfo page. 
Create a directory for the app and `cd` into it:

```bash
$ mkdir phpinfo
$ cd php-example
```

Create an `index.php` file with the following

```php
<?php 
  phpinfo();
```

### Deploy example application

**Deployment using Nginx**

* Nginx version 1.4.1
* PHP version 5.4.17
* Memcached version 1.0.7

```bash
truck push --buildpack https://github.com/iphoting/heroku-buildpack-php-tyler.git
```

**Deployment using Apache**

* Apache version 2.2.25
* PHP version 5.3.27

```bash
truck push --buildpack https://github.com/heroku/heroku-buildpack-php.git
```

## Database connection

Simple example to connect you application with the Trucker.io mysql service

```php
<?
  // Setup db connection
  $db      = parse_url($_SERVER['DATABASE_URL']);
  $db_name = trim($db['path'],'/');
  mysql_connect($db['host'], $db['user'], $db['pass']) or die('Could not connect');
  mysql_select_db($db_name);
  // Example query to db
  $query  = 'SELECT id, title, body FROM example_table';
  $result = mysql_query($query) or die('Query failed');
?>
```