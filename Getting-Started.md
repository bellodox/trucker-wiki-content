## Overview
Regardless of the type of application you want to deploy, you will need to perform the following steps, in order to get started with Trucker:

1. [[Create an Account|Getting Started#Create An Account]]
1. [[Install the Command Line Tool|Getting Started#Install the Command Line Tool]]
1. [[Log in Trucker|Getting Started#Log in Trucker]]

## Create An Account
To start using Trucker, you will need to sign up for an account, through [Trucker.io](https://trucker.io). 

<!-- We don't want this at this point
Alternatively, you can create an account using the [[Trucker Command Line Tool|Getting Started#install-trucker-command-line-tool]].

### Sign up using the trucker CLI
Target `truck` at the Cloud Controller for api.trucker.io:

```bash
$ truck target api.trucker.io
Setting target to https://api.trucker.io... OK
```

Create a user and login:
```bash
$ truck register [email]
```
-->

## Install the Command Line Tool

You can deploy and manage your applications right from the terminal, by using the [trucker-cli command line tool](http://rubygems.org/gems/trucker-cli), which is our own flavor of the [CloudFoundry client](https://github.com/cloudfoundry/cf-docs/blob/master/source/docs/using/managing-apps/cf/index.html.md).

Ruby and Rubygems are required to be present, since the `trucker-cli` tool is a Ruby gem. 

To install `trucker-cli`, type:
```
$ gem install trucker-cli
```

## Log in Trucker

You can create and manage applications using the  [Trucker control panel](https://cockpit.trucker.io). However you will need to also log in using the trucker command line client:

```bash
$ truck target https://api.ie.trucker.io
Setting target to https://api.ie.trucker.io... OK

$ truck login
email>
password>
```

At this point, the trucker client will require that you choose a space to work in. Spaces are enviroments whose purpose is to organize your applications based on their lifecycle stage. Your trucker account comes equipped with the following spaces:
```bash
1: development
2: production
3: staging
Space>
```
