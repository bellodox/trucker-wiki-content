## Overview
This section goes over the steps that are required in order to deploy your application, depending on the language and framework that it is using.

In general, there are two ways of deploying an application with Trucker:

* Use the [trucker command-line client].
* The second way would be deploying your application using our Trucker console. This is currently under active development. It's main current objective is to offer you the ability to monitor your deployments and visualize data, rather than let you deploy applications. The [trucker command-line client] can be potentially used to deploy many more types of applications.

<!-- 
## Using the trucker dashboard

The trucker dashboard is currently in active development. When it goes live, you will be able to use it to instantly launch several application types, with the following steps:

* Click 'Deploy new app'
* Select an application type from the grid menu
* Type in a domain name for your application
* Click 'Deploy'

After your application is done being deployed, you will be able to configure it. From the dashboard, you can perform  actions such as scaling your application, adding services to it (e.g, MySQL), or edit the application domains. 
-->

## Using the trucker command line client
As a prerequisite, you need to be logged in Trucker using the command line client. If not, see the [[Getting Started Guide|Getting-Started]].

### Does Trucker support my application?

On its own, Trucker is framework- and Runtime-agnostic. It deploys applications using buildpacks, which are packaged scripts that instruct the platform on how to stage applications using a specific Runtime/Framework. 

The buildpack model has been developed by Heroku, and it was made open-source, where it found the support of the CloudFoundry community. There are, therefore, many buildpacks that support a considerable amount of popular frameworks, and can be used out of the box with Trucker.

#### Official Support

Trucker **officially supports** the frameworks/runtimes listed below. Official support means that Trucker will automatically detect applications that use any of the frameworks listed here. 

| Runtime | Framework/CMS |
|:-------:|:---------:|
| Ruby    | [[Ruby on Rails]] |
|         | Rack |
|         | Sinatra |
| Node.js | [[Express]]|
|Java     | [[Main Class|Java#Main-Class]] |
|         | [[Spring|Java#Spring]] |
|         | [[Play|Java#Play]]   |

#### Community Support

If your application does not use any of the officially supported buildpacks, you might be able to find a suitable buildpack from the following sources:

* [CloudFoundry community buildpacks](https://github.com/cloudfoundry-community/cf-docs-contrib/wiki/Buildpacks). 
* [Heroku third-party buildpacks](https://devcenter.heroku.com/articles/third-party-buildpacks). It is not certain that these buildpacks will work on Trucker.
 

Among others, you can find buildpacks for the following runtimes/frameworks:

| Runtime | Framework/CMS |
|:-------:|:---------:|
| PHP     | [[Simple app|PHP]] |
|         | [[Wordpress]]  | 
|         | [[Drupal]] |
|         | [[Joomla!]]|
| Python  | [[Python]] |
| Static Site|[[Static Site|Nginx]]|


To deploy your application with one of these buildpacks, you will have to explicitly specify the buildpack source, during deployment:

```bash
$ truck push [appname] --buildpack=[git url]
```

#### Create your own buildpack

If there is no support for your application, you can ultimately [write your own buildpack!](https://github.com/cloudfoundry/cf-docs/blob/master/source/docs/using/deploying-apps/custom-buildpacks.html.md). 


### Deploy

Simply push your application to Trucker:
```bash
$ truck push
```

During the deployment, Trucker will request that you provide some configuration parameters:

```bash
Name> HelloWorldApp #Can contain alphanumeric and special chars, except spaces.
```

```bash
Instances> 1 #How many instances of your application do you need?

1: 128M       # You need to provide a memory limit for your application container. 
2: 256M       # Applications that exceed this limit are restarted, so make
3: 512M       # sure your application is provided with enough memory.
4: 1G                            
Memory Limit> 512M

Creating HelloWorldApp... OK

1: HelloWorldApp
2: none
Subdomain> helloworldapp    # The subdomain where your application will be available at

1: ie.trucker.io
2: none                   # The combination of the subdomain and the domain 
Domain> ie.trucker.io     # configuration will be the endpoint of the application

Creating route HelloWorldApp.ie.trucker.io... OK
Binding HelloWorldApp.ie.trucker.io to HelloWorldApp... OK

Create services for application?> n   # Does your application require a service (e.g, MySQL DB)?

Bind other services to application?> n

Save configuration?> y   # Save the configuration to a yml file

Saving to manifest.yml... OK
Uploading myRoRApp... OK
Preparing to start HelloWorldApp... OK
Checking status of app 'HelloWorldApp'...
  0 of 1 instances running (1 starting)
  1 of 1 instances running (1 running)
Push successful! App 'HelloWorldApp' available at helloworldapp.ie.trucker.io
```

## When your app fails to start

Sometimes your application might fail to run on Trucker. In that case, first you will have to make sure that it runs on your local machine.

Next step is to troubleshoot the application deployment on Trucker, using the command line client:
```bash
$ truck logs appname   # Show the stdout, stderr and environment 
                       # logs of your application
                       
Getting logs for appname #0... OK

Reading logs/env.log... OK

Reading logs/stderr.log... OK

Reading logs/stdout.log... OK
```

```bash
$ truck health appname # Is your application running?
Getting health status... OK

appname: running
```

```bash
$ truck stats appname # Retrieve info on the resource usage of your app
Getting stats for appname... OK

instance   cpu    memory         disk
0         0.0%   5.1M of 128M   79.9M of 1G
```

```bash
$ truck crashlogs appname # Show the logs of a crashed application instance
```

## Next Steps
You now know what the general deployment procedure looks like, but your application might require some special configuration to deploy, depending on the web framework/runtime it is using. Have a look at how we [[deploy applications that use various web frameworks|Guides-for-various-application-types]].

You might also be interested in [[deploying different services and bind them to your applications|Services]].


[trucker dashboard]: http://dashboard.ie.trucker.io
[trucker command-line client]: http://rubygems.org/gems/trucker
[1]: http://rubygems.org/gems/trucker
