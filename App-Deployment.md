Cloud Foundry supports many frameworks and runtimes. Learn about the preparations for each below:

###RUNTIME  FRAMEWORK
Javascript: Node.js
Java / JVM:  Java Spring, Grails, Scala Lift, and Play
Ruby: Rack, Rails, or Sinatra

Cloud Foundry supports these frameworks and runtimes using a buildpack model. Some of the Heroku third party buildpacks will work, but your experience may vary. To push an application using one of these buildpacks use `trucker push [appname] --buildpack=[git url]`

## Push Your Application to the Cloud
Before you deploy, you need to decide on the answers to some questions:

**Name:** You can use any series of alpha-numeric characters without spaces as the name of your application.

**Instances:** The number of instances you want running.

**Memory Limit:** The maximum amount of memory that each instance of your application is allowed to consume. If an instance goes over the maximum limit, it will be restarted. If it has to be restarted too often, it will be terminated. So make sure you are generous in your memory limit.

**Start Command:** This is the command that Cloud Foundry will use to start each instance of your application. The start command is specific to your framework.
* If you do not specify a start command when you push the application, Cloud Foundry will use the value of the `web` key in the `procfile` for the application, if it exists; failing that, Cloud Foundry will start the application using the value of the buildpack's web attribute of `default_process_types`.

**URL and Domain:** `trucker` will prompt you for both a URL and a domain. The URL is the subdomain for your application and it will be hosted at the primary domain you choose. The combination of the URL and domain must be globally unique.

**Services:** `trucker` will ask you if you want to create and bind one or more services such as MySQL or Redis to your application. For the purposes of this guide, you can answer no when prompted to add a service. Services are addressed in the next guide, Adding a Service.

You can define a variety of deployment options on the command line when you run `trucker push`, or in a manifest file. For more information:

* See the push section on "trucker Command Line Interface" for information about the `push` command and supplying qualifiers on the command line.
* See the trucker Push and the Manifest section on "Application Manifests" for information about using an application manifest to supply deployment options.

## An Example Transcript
Here is an example transcript from deploying a Ruby on Rails application. Note that in this example, we already provisioned an ElephantSQL instance and named it "elephantpg":

```
$ trucker push
Name> whiteboard

Instances> 1

1: 64M
2: 128M
3: 256M
4: 512M
5: 1G
6: 2G
Memory Limit> 256M

Creating whiteboard... OK

1: whiteboard
2: none
Subdomain> whiteboard

1: truckerapps.io
2: none
Domain> truckerapps.io

Creating route whiteboard.truckerapps.io... OK
Binding whiteboard.truckerapps.io to whiteboard... OK

Create services for application?> n

Bind other services to application?> y

1: elephantpg
Which service?> 1

Binding elephantpg to whiteboard... OK
Save configuration?> n

Uploading whiteboard... OK
Starting whiteboard... OK
-----> Downloaded app package (224K)
Installing ruby.
-----> Using Ruby version: ruby-1.9.2
-----> Installing dependencies using Bundler version 1.3.2
       Running: bundle install --without development:test --path vendor/bundle --binstubs vendor/bundle/bin --deployment
       ...
       Your bundle is complete! It was installed into ./vendor/bundle
       Cleaning up the bundler cache.
-----> Writing config/database.yml to read from DATABASE_URL
-----> Preparing app for Rails asset pipeline
       Running: rake assets:precompile
       Asset precompilation completed (41.70s)
-----> Rails plugin injection
       Injecting rails_log_stdout
       Injecting rails3_serve_static_assets
-----> Uploading staged droplet (41M)
-----> Uploaded droplet
Checking whiteboard...
Staging in progress...
Staging in progress...
Staging in progress...
Staging in progress...
Staging in progress...
Staging in progress...
Staging in progress...
Staging in progress...
Staging in progress...
  0/1 instances: 1 starting
  0/1 instances: 1 starting
  0/1 instances: 1 starting
  0/1 instances: 1 starting
  0/1 instances: 1 starting
  0/1 instances: 1 starting
  0/1 instances: 1 starting
  0/1 instances: 1 starting
  0/1 instances: 1 starting
  0/1 instances: 1 starting
  0/1 instances: 1 starting
  0/1 instances: 1 starting
  0/1 instances: 1 starting
  0/1 instances: 1 starting
  1/1 instances: 1 running
OK
```

## Troubleshooting
If your application does not start on Cloud Foundry, it's a good idea to double-check that your application can run locally.

You can troubleshoot your application in the cloud using `trucker`.

To check the health of your application, use

```
trucker health appname
```

To check how much memory your application is using:

```
trucker stats appname
```

To see the environment variables and recent log entries:

```
trucker logs appname
```

To tail your logs:

```
trucker tail appname
```

If your application has crashed and you cannot retrieve the logs with cf logs, you can retrieve its dying words with:

```
trucker crashlogs appname
```

## Next Step - Binding a service
Binding and using a service is covered in our guide, Adding a Service.