## Overview
This section goes over the steps that are required in order to deploy your application, depending on the language and framework that it is using.

In general, there are two ways of deploying an application with Trucker:
* Use the [trucker command-line client].
* Use the [trucker dashboard] to one-click deploy an application from a selection of runtimes and web-frameworks. We are striving towards adding more one-click-deploy applications in the dashboard. If you cannot find what you are looking for in the one-click deploy applications, you can always use the command-line client!
 
The dashboard is currently in active development. It's main current objective is to offer you the ability to monitor your deployments and visualize data, rather than let you deploy applications (even though it is still capable of that). The [trucker command-line client] can be potentially used to deploy many more types of applications.

## Using the trucker dashboard

Several applications can be instantly launched through the [trucker dashboard], with the following steps:
* Click 'Deploy new app'
* Select an application type from the grid menu:

![Select Application Type](http://webfront.ie.trucker.io/assets/images/trucker-io-logo.png)

* Type in a domain name for your application:

![Select Application Type](http://webfront.ie.trucker.io/assets/images/trucker-io-logo.png)
* Click 'Deploy'

After your application is done being deployed, you will be able to configure it. From the dashboard, you can perform  actions such as scaling your application, adding services to it (e.g, MySQL), or edit the application domains.

## Using the trucker command line client
As a prerequisite, you need to be logged in Trucker using the command line client. If not, see the [[Getting Started Guide|Getting-Started]].

### Does Trucker support my application?

On its own, Trucker is framework- and Runtime-agnostic. It deploys applications using [[buildpacks]], which are packaged scripts that instruct the platform on how to stage applications using a specific Runtime/Framework. 

The buildpack model has been developed by Heroku, and it was made open-source, where it found the support of the CloudFoundry community. There are, therefore, many buildpacks that support a considerable amount of popular frameworks, and can be used out of the box with Trucker.

Trucker **officially supports** the frameworks/runtimes listed below:

* Ruby: 
  * [[Rails|Rails-3]] 
  * [[Rack|Rack]]
  * [[Sinatra|Sinatra]]
* Java:
  * [[Java Spring|Java]] 
  * [[Grails|Java]] 
  * [[Scala Lift|Java]] 
  * [[Play|Java]]
* Javascript: 
  * [[Node.js|Nodejs]]
 
Trucker will automatically detect applications that use any of the frameworks listed above. 

#### Other Buildpacks

If your application does not use any of the officially supported buildpacks, you might be able to find a suitable buildpack from the following sources:
* [CloudFoundry community buildpacks](https://github.com/cloudfoundry-community/cf-docs-contrib/wiki/Buildpacks). 
* [Heroku third-party buildpacks](https://devcenter.heroku.com/articles/third-party-buildpacks). It is not certain that these buildpacks will work on Trucker.

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
2: 256M       # Applications that exceed this limit, are restarted, so make  memory.
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
Binding HelloWorldApp.ie.trucker.io to myRoRApp... OK

Create services for application?> n   # Does your application require a service (e.g, MySQL DB)?

Bind other services to application?> n

Save configuration?> y   # Save the configuration in 

Saving to manifest.yml... OK
Uploading myRoRApp... OK
Preparing to start HelloWorldApp... OK
Checking status of app 'HelloWorldApp'...
  0 of 1 instances running (1 starting)
  1 of 1 instances running (1 running)
Push successful! App 'HelloWorldApp' available at helloworldapp.ie.trucker.io
```

## Choosing your deployment parameters

Upon deployment of your application, the trucker command line client will ask you to provide some basic input regarding the application configuration:



**Memory Limit:** The maximum amount of memory that each instance of your application is allowed to consume. If an instance goes over the maximum limit, it will be restarted. If it has to be restarted too often, it will be terminated. So make sure you are generous in your memory limit.

**Start Command:** This is the command that Trucker will use to start each instance of your application. The start command is specific to your framework.
* If you do not specify a start command when you push the application, Trucker will use the value of the `web` key in the `procfile` for the application, if it exists; failing that, Trucker will start the application using the value of the buildpack's web attribute of `default_process_types`.

**URL and Domain:** `truck` will prompt you for both a URL and a domain. The URL is the subdomain for your application and it will be hosted at the primary domain you choose. The combination of the URL and domain must be globally unique.

**Services:** `truck` will ask you if you want to create and bind one or more services such as MySQL or Redis to your application. For the purposes of this guide, you can answer no when prompted to add a service. Services are addressed in the next guide, Adding a Service.

You can define a variety of deployment options on the command line when you run `truck push`, or in a manifest file. For more information:

* See the push section on "trucker Command Line Interface" for information about the `push` command and supplying qualifiers on the command line.
* See the trucker Push and the Manifest section on "Application Manifests" for information about using an application manifest to supply deployment options.


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


[trucker dashboard]: http://dashboard.ie.trucker.io
[trucker command-line client]: http://rubygems.org/gems/trucker
[1]: http://rubygems.org/gems/trucker