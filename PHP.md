### Table of Contents

* [[Persistent data storage|PHP#persistent-data-storage]]
* [[Example application|PHP#example-application]]
* [[Database connection|PHP#database-connection]]

## Persistent data storage

Trucker.io does not yet have a persistent data storage system, though we're working on it. [Read more](Limitations#persistent-data-storage)

## Example application

Create a directory for the app and `cd` into it

```
$ mkdir php-example
$ cd php-example
```

Create an `index.php` file with the following

```
<?php echo "Hello world!"; ?>
```

### Deploy example application

**Deployment using Nginx**

* Nginx version 1.4.1
* PHP version 5.4.17
* Memcached version 1.0.7

```
truck push --buildpack https://github.com/iphoting/heroku-buildpack-php-tyler.git
```

**Deployment using Apache**

* Apache version 2.2.25
* PHP version 5.3.27

```
truck push --buildpack https://github.com/heroku/heroku-buildpack-php.git
```

## Database connection

Simple example to connect you application with the Trucker.io mysql service

```
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