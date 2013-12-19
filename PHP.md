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

The installation process has created some files in the deployed application container, but we need to make sure
that these files are persisted. Without persisting these files, then everytime the application is restarted or 
scaled, Joomla will go through the installation process.  

**Before you remove the installation folder**, you will need to download the `configuration.php` file from Trucker:
```bash
$ truck files joomla-test app/configuration.php

<?php
class JConfig {
	public $offline = '0';
	public $offline_message = 'This site is down for maintenance.<br /> Please check back again soon.';
  ...
}
```
Copy the output of the command above, and create a configuration.php file that contains the Joomla configuration.
Next delete the local installation folder, as it will not be further required:
```bash
$ rm -rf installation
```

The final step is to remove the currently pushed joomla application, and redeploy. This time, the Joomla configuration
will be permanent, and it will require no further configuration.

```bash
$ truck delete -f joomla-test # The MySQL service is not deleted
$ truck push 
```


## Drupal

To start with, download and unzip Drupal:
```bash
$ curl -O http://ftp.drupal.org/files/projects/drupal-7.23.tar.gz
$ tar -xzf drupal-7.23.tar.gz
$ rm drupal-7.23.tar.gz
$ cd drupal-7.23
$ cp sites/default/default.settings.php sites/default/settings.php
```

Next, the database endpoint and credentials need to be configured. The VCAP_SERVICES environment variable can be used by 
the deployed application on Trucker, to retrieve the database information.

Edit site/default/settings.php and replace `"$database = array()"` with:
```php
$db = parse_url($_SERVER['DATABASE_URL']);
$db_name = trim($db['path'],'/');

$databases['default']['default'] = array(
'driver' => 'mysql',
'database' => $db_name,
'username' => $db['user'],
'password' => $db['pass'],
'host' => $db['host'],
'prefix' => 'main_',
'collation' => 'utf8_general_ci',
);
```
The database should now be configured. The next step is to push the application to Trucker:

```bash
$ truck push --buildpack https://github.com/trucker/heroku-buildpack-php-tyler.git
Name> drupal-test

Instances> 1

1: 128M
2: 256M
3: 512M
4: 1G
Memory Limit> 256M

Creating drupal-test... OK

1: drupal-test
2: none
Subdomain> drupal-test

1: ie.trucker.io
2: none
Domain> ie.trucker.io

Creating route drupal.ie.trucker.io... OK
Binding drupal.ie.trucker.io to joomla... OK

Create services for application?> y

1: rds-mysql n/a, via aws
What kind?> 1

Name?> rds-mysql-8ba15

1: 100mb: Shared service, 100MB storage, 10 connections
Which plan?> 1

Creating service rds-mysql-8ba15... OK
Binding rds-mysql-8ba15 to drupal-test... OK
Create another service?> n

Bind other services to application?> n

Save configuration?> y

Saving to manifest.yml... OK
Uploading drupal-test... OK

Preparing to start drupal-test... OK
-----> Downloaded app package (3.5M)
-----> Downloaded app buildpack cache (36M)
Initialized empty Git repository in /tmp/buildpacks/heroku-buildpack-php-tyler.git/.git/
-----> Fetching Manifest
       https://s3.amazonaws.com/heroku-buildpack-php-tyler/manifest.md5sum
-----> Installing Nginx
       Using cached Nginx v1.4.1
-----> Installing libmcrypt
       Using cached libmcrypt v2.5.8
-----> Installing libmemcached
       Using cached libmemcached v1.0.7
-----> Installing PHP
       Using cached PHP v5.4.17
-----> Installing newrelic
       Using cached newrelic daemon v2.9.5.78
-----> Copying config files
-----> Installing boot script
-----> Done with compile
-----> Uploading droplet (40M)
Checking status of app 'drupal-test'...
  1 of 1 instances running (1 running)
Push successful! App 'drupal-test' available at http://drupal-test.ie.trucker.io
```

Drupal is now running, but the installation process needs to be completed. Point your browser to the installation script, 
http://drupal-test.ie.trucker.io/install.php, and follow the instructions to complete the installation procedure.

### Persistent Drupal filesystem in AWS S3

Currently, Trucker has no support for persistent storage, and Drupal's file-system is based on local storage by default. As a result, any files saved while Drupal is running on Trucker will be lost when the instance is stopped. Moreover, if the drupal application is scaled out to several instances, the newly allocated instances will be missing all the files that were added to the CMS. 

We therefore need to configure our Drupal application to use a cloud storage solution, such as Amazon Simple Storage Service (S3). We will need to install the [Amazon S3 module](https://drupal.org/project/amazons3) to Drupal. This Drupal configuration needs to happen **before** the application is pushed to Trucker, so that when we finally upload it to Trucker, it will already be configured to use Amazon S3. The process that we need to follow is described in the steps below:

* Set up Drupal on localhost. We need to follow the [official guide](https://drupal.org/documentation/install) on how to set up our webserver and database. 
* Next, we would like to install the [AmazonS3 plugin](https://drupal.org/project/amazons3). The plugin requires two more modules to be installed, the [libraries API](https://drupal.org/project/libraries) and the [AWS SDK for PHP](https://drupal.org/project/awssdk). Download these plugins, unzip them, and place them in sites/all/modules. You will also need to clone the repository git@github.com:aws/aws-sdk-php.git and place it in sites/all/libraries.
* We have identified an issue with the AmazonS3 plugin, where the file paths are not properly formed. We will therefore need to edit profiles/standard/standard.install, and change the "uri_scheme" property of array "field", from "public" to "s3". If we already have an installed drupal application, we will have to change the value of "uri_scheme" from within the database, in table "field_config". After we have performed the change, we should make sure that the value of "uri_scheme" is not cached in table "cache_field".
* We need to perform the Drupal installation through the browser (e.g., http://localhost/install.php), with the standard installation.
* We will have to enable the installed modules (AWSSDK, libraries, AmazonS3) in admin/modules, and then configure them from admin/config. Namely, the AWSSDK plugin requires that we fill in the credentials of our AWS account, and AmazonS3 requires that we provide it with a bucket name (the bucket should already exist on S3).
* We need to configure the drupal File-system in admin/config/media/file-system to use Amazon S3 as plugin. We have to change the Default download method to Amazon S3.

Our local Drupal setup should now be using S3 as a filesystem. We proceed with uploading our application to Trucker:
* We make sure that our settings.php file has the proper database configuration for trucker, as described above.
* We push the application to trucker, as described above.
* Finally, we have to upload our local drupal database contents to our Trucker database. The process requires that we dump our local db contents to a file, create a tunnel to the remote database through trucker, and upload the file contents to the remote database:
```bash
$ mysqldump local-drupal-db-name > drupal.sql
```
The remote database credentials can be retrieved when creating a tunnel to the service:

```bash
$ trucker tunnel name-of-database-service
Opening tunnel on port 10000... OK

Service connection info:
  username : remote_user
  password : remote_password
  name     : remote_db_name
  uri      : mysql://remote_user:remote_password@mysqldb.xxxxxxx.eu-west-1.rds.amazonaws.com:3306/remote_db_name

Open another shell to run command-line clients or
use a UI tool to connect using the displayed information.
Press Ctrl-C to exit...
```

We can then populate the remote database:
```bash
$ mysql -uremote_user -p remote_db_name < drupal.sql
Password: remote_password
```

Our remote DB should now have the drupal configuration and contents that we have performed locally, and our Drupal Application on trucker should now work with Amazon S3.