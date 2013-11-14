### Table of Contents

* [[Example application|PHP#example-application]]
* [[Database connection|PHP#database-connection]]

Trucker.io does not yet have a persistent data storage system, though we're working on it.
[Read more](Limitations#persistent-data-storage)

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

## Joomla!
The default heroku PHP buildpack is not sufficient to deploy Joomla! because of a missing Zlib dependency. Therefore, the iphoting php buildpack is used instead:

```bash
$ truck push --buildpack https://github.com/trucker/heroku-buildpack-php-tyler.git
Name> joomla-test

Instances> 1

1: 128M
2: 256M
3: 512M
4: 1G
Memory Limit> 256M

Creating joomla-test... OK

1: joomla-test
2: none
Subdomain> joomla-test

1: ie.trucker.io
2: none
Domain> ie.trucker.io

Creating route joomla.ie.trucker.io... OK
Binding joomla.ie.trucker.io to joomla... OK

Create services for application?> y

1: rds-mysql n/a, via aws
What kind?> 1

Name?> rds-mysql-8ba15

1: 100mb: Shared service, 100MB storage, 10 connections
Which plan?> 1

Creating service rds-mysql-8ba15... OK
Binding rds-mysql-8ba15 to joomla-test... OK
Create another service?> n

Bind other services to application?> n

Save configuration?> y

Saving to manifest.yml... OK
Uploading joomla-test... OK
Preparing to start joomla-test... OK
-----> Downloaded app package (9.1M)
Initialized empty Git repository in /tmp/buildpacks/heroku-buildpack-php-tyler.git/.git/
-----> Fetching Manifest
       https://s3.amazonaws.com/heroku-buildpack-php-tyler/manifest.md5sum
-----> Installing Nginx
       Bundling Nginx v1.4.1
       https://s3.amazonaws.com/heroku-buildpack-php-tyler/nginx-1.4.1-heroku.tar.gz
-----> Installing libmcrypt
       Bundling libmcrypt v2.5.8
       https://s3.amazonaws.com/heroku-buildpack-php-tyler/libmcrypt-2.5.8.tar.gz
-----> Installing libmemcached
       Bundling libmemcached v1.0.7
       https://s3.amazonaws.com/heroku-buildpack-php-tyler/libmemcached-1.0.7.tar.gz
-----> Installing PHP
       Bundling PHP v5.4.17
       https://s3.amazonaws.com/heroku-buildpack-php-tyler/php-5.4.17-with-fpm-heroku.tar.gz
-----> Installing newrelic
       Bundling newrelic daemon v2.9.5.78
       https://s3.amazonaws.com/heroku-buildpack-php-tyler/newrelic-2.9.5.78-heroku.tar.gz
-----> Copying config files
-----> Installing boot script
-----> Done with compile
-----> Uploading droplet (43M)
Checking status of app 'joomla-test'...
  0 of 1 instances running (1 starting)
  1 of 1 instances running (1 running)
Push successful! App 'joomla-test' available at http://joomla-test.ie.trucker.io
```

Joomla! should now be running. The next step involves setting it up through the browser. This process requires the database endpoint and credentials. They can be identified by performing the following steps:

1. Find the globally unique identifier (GUID) of the application:
```bash
$ truck app -t joomla-test
...
  "entity": {
    "app_guid": "9e0695bc-c5a9-4c55-b1a3-5cce3ee09805",
    "service_instance_guid": "e3f43673-3447-43bf-8f8b-c73abe7ea96b",
    "credentials": "[PRIVATE DATA HIDDEN]",
    "binding_options": {
    },
    "gateway_data": {
      "data": {
        "binding_options": {
        }
      }
    },
...
```
1. Using the app guid, find the service bindings for the app:
```bash
$ truck curl get /v2/apps/9e0695bc-c5a9-4c55-b1a3-5cce3ee09805/service-bindings
```bash
...
  "entity": {
  "app_guid": "9e0695bc-c5a9-4c55-b1a3-5cce3ee09805",
  "service_instance_guid": "e3f43673-3447-43bf-8f8b-c73abe7ea96b",
  "credentials": {
    "jdbcUrl": "jdbc:mysql://b7ecce832f7130:addb767f@mysqldb.cb1frtdjjo2d.eu-west-1.rds.amazonaws.com:3306/ad_3ee916e51cdecd4",
    "uri": "mysql://b7ecce832f7130:addb767f@mysqldb.cb1frtdjjo2d.eu-west-1.rds.amazonaws.com:3306/ad_3ee916e51cdecd4?reconnect=true",
    "name": "ad_3ee916e51cdecd4",
    "hostname": "mysqldb.cb1frtdjjo2d.eu-west-1.rds.amazonaws.com",
    "port": "3306",
    "username": "b7ecce832f7130",
    "password": "addb767f"
  },
...        
```
The installation procedure can then continue on the joomla web-UI:
![Joomla Installation procedure](images/joomla.png)

