Trucker allows you to deploy applications without worrying about configuration headaches, making it faster and easier to build, test, deploy and scale your app.

This guide walks you through binding and using services at run.pivotal.io. It assumes you have pushed an application to run.pivotal.io. If you haven't done, that, you might consider going through our guide, [[Getting Started|Getting-Started]]. 

## Intro to Services

Trucker Services are add-ons that can be provisioned alongside your application.

In order to use services with your application you will need to:

1. [Create a service instance](Services#Creating-service-instances)
1. [Binding a service instance to your application](Services#Binding-a-service-instance-to-your-application)
1. [Using service instances with your application](Services#Using-service-instances-with-your-application)

### Services vs. Service instances
Services provision services instances. As an example, ClearDB is a service which provisions MySQL databases. Depending on the plan you select, you might get a database in a multi-tenant server, or a dedicated server. But not all services provide databases; a service may simply provision an account on their system for you. Whatever is provisioned for you we refer to as a service instance.  

## Creating service instances

You can create a service instance with the command, `truck create-service`.

```
$ truck create-service
1: mysql
2: ...
What kind?> 1
```

After you choose a service, `truck` will ask you to provide a name for your service instance. This is an alias for the instance which is meaningful to you. `truck` will provide a default for you, or you can enter any series of alpha-numeric characters ([a-z], [A-Z], [0-9]) plus hyphens (-) or underscores (_). You can rename the instance later at any time.

```
Name?> mysql-a1234
```

Next you will be asked to select a plan. Service plans are a way for providers to offer varying levels of resources or features for the same service.

```
1: 100mb
2: ...
Which plan?> 
```

Following this step, your service instance will be provisioned.

```
Creating service mysql-a1234... OK
```

## Binding a service instance to your application

Not all services provide bindable service instances. Binding requires an additional level of integration with a provider, and it doesn't make sense in all cases for the provider to return any credentials for an application's use.

For services that offer bindable service instances, the binding operation puts credentials for the service instance in the environment variable VCAP_SERVICES, where your application can consume them. For more information, see [VCAP_SERVICES Environment Variables](Environment-Variables).

You can bind a service to an application with the command `truck bind-service`. You will first be asked for the application to bind the service to.

```
$ truck bind-service
1: myapp
Which application?> 1
```

Next you will be asked for the service instance you want to bind.

```
1: mysql-a1234
Which service?> 1
```

If the service supports binding, your service instance will then be bound to your app.

```
Binding mysql-a1234 to myapp... OK
```

## Using service instances with your application

Once you have a service instance created and bound to your app, you will need to configure your application to use the correct credentials for your service.

There are three ways of consuming service instance credentials within your application.

| Binding Strategy     | Description                                                                                                                            |
| :------------------- | :--------------------                                                                                                                  |
| Auto-configuration | Trucker creates a service connection for you.                                                                |
| cfruntime            | Creates an object with the location and settings of your services. Set your service connections based on the values in that object.    |
| Manual               | Parse the JSON credentials object yourself from the [VCAP_SERVICES Environment Variables](Environment-Variables). |
 
## <a id='configuring'></a>Configuring Connections for Different Frameworks ##
 
The procedure for configuring an application to connect to a service varies by framework. Note that auto-configuration is not supported for all frameworks. The table below has links to instructions for configuring service connections in different environments.  

| Runtime               | Framework                   |Auto-Configuration Support|
| :-------------        |:-------------               |:-------------            |
| Java / JVM            | <li>Spring <li>Grails <li>Lift | Supported. |
| Ruby                  | <li>Rack, Rails, or Sinatra        | Supported only for Rails, with some limitations. | 
| Javascript            | <li>Node.js | Not yet available in Trucker. |