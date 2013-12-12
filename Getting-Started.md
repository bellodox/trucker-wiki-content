## Overview
Regardless of the type of application you want to deploy, you will need to perform the following steps, in order to get started with Trucker:

1. [[Create an Account|Getting Started#Create An Account]]
2. [[Install-the-Command-Line-Tool]]
3. [Log in]

## Create An Account
To start using Trucker, you will need to sign up for an account, through [Trucker.io](https://trucker.io). 

<!-- We don't want this at this point
Alternatively, you can create an account using the [[Trucker Command Line Tool|Getting Started#install-trucker-command-line-tool]].

### Sign up using the trucker CLI
Target `truck` at the Cloud Controller for api.trucker.io:

```
$ truck target api.trucker.io
Setting target to https://api.trucker.io... OK
```

Create a user and login:
```
$ truck register [email]
```
-->

## Install the Command Line Tool

You can deploy and manage your applications right from the terminal, by using the [trucker-cli command line tool](http://rubygems.org/gems/trucker-cli), which is our own flavor of the [CloudFoundry client](https://github.com/cloudfoundry/cf-docs/blob/master/source/docs/using/managing-apps/cf/index.html.md).

Ruby and Rubygems are required to be present, since the `trucker-cli` tool is a Ruby gem. 
See the [Installing Ruby](installing-ruby.md) page for help installing Ruby and RubyGems.

To install `trucker-cli`, simply type the following at your command line:
```
$ gem install trucker-cli
```


### Login in using the Trucker CLI
```
$ truck login
email>
password>
```


When the gem has completed installing you should see a message that says, "Successfully installed." You can now target `truck` at the Cloud Controller for api.trucker.io:

```
$ truck target api.trucker.io
Setting target to https:/api.trucker.io... OK
```

Then log in:
```
$ truck login
email>
password>
```

When you log in, `truck` will prompt you to choose a space. In v2 Cloud Foundry, a space is a container for an application and all its associated processes. By default, your account has three spaces:
```
1: development
2: production
3: staging
Space>
```
You can choose any of these spaces to deploy your application.
