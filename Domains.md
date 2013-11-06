## Key Facts About Domains
In Trucker, domains are associated with spaces.

A Trucker instance defines a default domain that is available to all spaces. For example the trucker.io domain is automatically mapped to all spaces in all organizations.

Trucker also supports custom domains — you can map a registered domain of your own to a space in Trucker, as described below.

## Map a Custom Domain to a Space

If you want use a registered domain of your own, you must define it in Trucker and map it the application’s space with the `truck map-domain` command.

The command below maps the custom domain `example.com` to the `development` space.

```
truck map-domain --space development example.com
```

If you do not want the application to be available via a URL, do not assign a domain to the application.

## View Domains for a Space

You can see domains that are mapped to a space using the `truck domains` command. In this example, two domains are mapped to the `development` space: the default `trucker.io` domain and the and custom `example.com` domain:

```
truck push my-new-app
truck  domains --space development
Getting domains in development... OK

name           owner   
trucker.io     none    
example.com    jdoe
```

## Unmap a Domain

You can unmap a domain with the `truck unmap-domain` command. In this example, the `example.com` domain is unmapped from the `development` space:

```
truck unmap-domain --space development example.com
Unmapping example.com from development... OK
```

## Map custom domain to application

First make sure your custom domain is mapped to your space. 
Map a custom domain to a space

Map application with custom domain:

```
truck map myapp example.com

Creating route .example.com... OK
Binding .example.com to myapp... OK
```

You can also map www to your application:

```
truck map --app myapp --host www --domain example.com 

Binding www.example.com to myapp... OK
```

### DNS settings

To make the application available on the custom domain you will need to forward the domain to the correct CNAME. First create an `A` record entry for example.com to point to the CNAME that `api.trucker.io` resolves to (you can find this using `ping` or `nslookup`).

Mapping `www` to your application can also be done. First create an `CNAME` record entry for example.com to point `www` to what `api.trucker.io` resolves to. It could be something like `run-trucker-elb-1234567890.eu-west-1.elb.amazonaws.com`.

And that's it, your domain is now mapped to your application.

## Key Facts About Subdomains

In some cases, defining the subdomain portion of a route is optional, but generally speaking this segment of a route is required to ensure that the route is unique. 
Note in particular that you must define a subdomain:

* If you assign the default domain defined for your Trucker instance (for example, trucker.io).
* If you assign a custom domain to the application, and that domain is, or will be, assigned to other applications. Note that in the case of a custom domain, a subdomain for it must be registered with your naming service along with the top-level domain.

You might choose not to assign a subdomain to an application that will not accept browser requests, or if you are using a custom domain for a single application only.

## Assign Domain and Subdomain at push Time

When you run `truck push` interactively, it prompts you to supply a subdomain and domain for the application. In the example dialog below, note that the:

* The options for subdomain are “myapp,” the value supplied earlier in the dialog for application name, and “none”. You can also enter a string at the prompt.
* The options for domain are (1) `trucker.io`, the default domain for the Cloud Foundry instance, (2) `example.com`, a custom domain previously mapped to the space, and (3) “none”.

The route created for the application as a result of the selections made below is:

```
myapp.example.com
```

```
truck push
Name> myapp

Instances> 1

1: 64M
2: 128M
3: 256M
4: 512M
Memory Limit> 256M

Creating myapp... OK

1: myapp
2: none
Subdomain> 1     

1: trucker.io
2: example.com
3: none
Domain> 2

Creating route myapp.example.com... OK
Binding myapp.example.com to myapp... OK
```

## Assign Subdomain in Manifest
If you create or edit the manifest for an application, you can use the `host` (for subdomain) and `domain` attributes to define the components of the application’s route.

## List Routes
You can list routes for the current space with `truck routes` command. Note that the subdomain is shown as “host”, separate from the domain segment. For example:

```
truck routes
Getting routes... OK

host                     domain

myapp                    example.com 
sinatra-hello-world      trucker.io
```

## Define or Change a Route from Command Line

You can assign or change the route for an application with the `truck map` command. For example, this command maps the route `myapp.example.com` to the application named “myapp”:

```
truck map --app myapp --host myapp --domain example.com 
```

Note that you use the `--host` qualifier to specify the subdomain.

If the application is running when you map a route, restart the application. The new route will not be active until the application is restarted.