## Prepare Your Application for Deployment
Trucker supports many frameworks and runtimes. Learn about the preparations for each below:

###RUNTIME  FRAMEWORK
| Runtime        | Framework                                                                             |
| :------------- | :-------------                                                                        |
| Javascript     | [[Node.js|Nodejs]]                                                                    |
| Java / JVM     | <li>[[Java Spring|Java]] <li>[[Grails|Java]] <li>[[Scala Lift|Java]] <li>[[Play|Java]]|
| Ruby           | <li>[[Rails|Rails-3]] <li>[[Rack|Rack]] <li>[[Sinatra|Sinatra]]                       |
| PHP            | [[PHP|PHP]]                                                                           |
| Static Content | [[Nginx|Nginx]]                                                                       |

Trucker supports these frameworks and runtimes using a buildpack model. Some of the Heroku third party buildpacks will work, but your experience may vary. To push an application using one of these buildpacks use `truck push [appname] --buildpack=[git url]`

## Push Your Application to the Cloud
Before you deploy, you need to decide on the answers to some questions:

**Name:** You can use any series of alpha-numeric characters without spaces as the name of your application.

**Instances:** The number of instances you want running.

**Memory Limit:** The maximum amount of memory that each instance of your application is allowed to consume. If an instance goes over the maximum limit, it will be restarted. If it has to be restarted too often, it will be terminated. So make sure you are generous in your memory limit.

**Start Command:** This is the command that Trucker will use to start each instance of your application. The start command is specific to your framework.
* If you do not specify a start command when you push the application, Trucker will use the value of the `web` key in the `procfile` for the application, if it exists; failing that, Trucker will start the application using the value of the buildpack's web attribute of `default_process_types`.

**URL and Domain:** `truck` will prompt you for both a URL and a domain. The URL is the subdomain for your application and it will be hosted at the primary domain you choose. The combination of the URL and domain must be globally unique.

**Services:** `truck` will ask you if you want to create and bind one or more services such as MySQL or Redis to your application. For the purposes of this guide, you can answer no when prompted to add a service. Services are addressed in the next guide, Adding a Service.

You can define a variety of deployment options on the command line when you run `truck push`, or in a manifest file. For more information:

* See the push section on "trucker Command Line Interface" for information about the `push` command and supplying qualifiers on the command line.
* See the trucker Push and the Manifest section on "Application Manifests" for information about using an application manifest to supply deployment options.

## An Example Transcript
Here is an example transcript from deploying a Ruby on Rails application.

```
$ truck push
Name> trucker-test

Instances> 1

1: 128M
2: 256M
3: 512M
4: 1G
Memory Limit> 256M

Creating trucker-test... OK

1: trucker-test
2: none
Subdomain> trucker-test

1: trucker.io
2: none
Domain> trucker.io

Creating route trucker-test.trucker.io... OK
Binding trucker-test.trucker.io to trucker-test... OK

Create services for application?> y

1: mysql
2: ...

What kind?> 1

Name?> mysql-ab1234

Creating service mysql-ab1234... OK
Binding mysql-ab1234 to trucker-test... OK
Create another service?> n

Uploading trucker-test... OK
Preparing to start trucker-test... OK
-----> Downloaded app package (40K)
-----> Using Ruby version: ruby-1.9.3
-----> Installing dependencies using Bundler version 1.3.2
       Running: bundle install --without development:test --path vendor/bundle --binstubs vendor/bundle/bin --deployment
       Fetching gem metadata from https://rubygems.org/..........
       Fetching gem metadata from https://rubygems.org/..
       Installing rake (10.1.0)
       ...
       Your bundle is complete! It was installed into ./vendor/bundle
-----> Writing config/database.yml to read from DATABASE_URL
-----> Preparing app for Rails asset pipeline
       Running: rake assets:precompile
       Asset precompilation completed (9.59s)
-----> Rails plugin injection
       Injecting rails_log_stdout
       Injecting rails3_serve_static_assets
-----> Uploading droplet (41M)
Checking status of app 'trucker-test'...
  1 of 1 instances running (1 running)
Push successful! App 'trucker-test' available at http://trucker-test.trucker.io
```

## Troubleshooting
If your application does not start on Trucker, it's a good idea to double-check that your application can run locally.

You can troubleshoot your application in the cloud using `truck`.

To check the health of your application, use

```
truck health appname
```

To check how much memory your application is using:

```
truck stats appname
```

To see the environment variables and recent log entries:

```
truck logs appname
```

To tail your logs:

```
truck tail appname
```

If your application has crashed and you cannot retrieve the logs with cf logs, you can retrieve its dying words with:

```
truck crashlogs appname
```