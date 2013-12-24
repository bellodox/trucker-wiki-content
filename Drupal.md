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
