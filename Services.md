## Overview

Trucker offers a collection of services that you can add to your applications. Services provide all kinds of functionality to your applications, like storing or caching data, application monitoring and analytics, emailing and more.

To use a service, you will have to:

1. [Create a service instance](Services#Creating-service-instances)
1. [Bind the instance to your application](Services#Bind-a-service-instance-to-an-application)
1. [Use the service instance](Services#Use-the-service-instance)
 

## Creating service instances

The Trucker dashboard is currently in active development. Its purpose is to provide you with an easy and fast way of creating service instances, binding and unbinding them to/from your applications, and monitoring them. In the meantime, you can use the command line client to manage your service instances.

You can create a service instance with the command, `truck create-service`.

```bash
$ truck create-service
1: mysql
2: redis
What kind?> 1
```

You will have to provide a name and service plan for your service instance:
```bash
Name?> mysql-a1234

1: 100mb
2: ...
Which plan?> 100mb

Creating service mysql-a1234... OK
```

## Bind a service instance to an application

Binding your service instance to an application means that your application is provided with some credentials, that it can use to perform requests to the service.  These credentials can be accessed by the application  through the VCAP_SERVICES variable

You can bind a service to an application with the command `truck bind-service`.

```bash
$ truck bind-service
1: myapp
Which application?> 1

1: mysql-a1234
Which service?> 1

Binding mysql-a1234 to myapp... OK
```

## Use the service instance

The final step is to use the service instances that are bound to your application, by configuring your application to use the correct credentials for your service instances.
