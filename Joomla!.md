## Prerequisites

You should have a Trucker account, the trucker command line client installed on your machine, and logged in the Trucker service. If not, have a look at our [[getting started]] section.

## Deploy Joomla! Application

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