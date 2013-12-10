**Table of Contents** 
---
- [Create An Account](#markdown-header-create-an-account)
	- [Sign up using the trucker CLI](#markdown-header-sign-up-using-the-trucker-cli)
	- [Login in using the Trucker CLI](#markdown-header-login-in-using-the-trucker-cli)
- [Install Trucker Command Line Tool](#markdown-header-install-trucker-command-line-tool)

## Create An Account
To start using Trucker, you will need to sign up for an account, through [Trucker.io](https://trucker.io). Alternatively, you can create an account using the [[Trucker Command Line Tool|Getting Started#install-trucker-command-line-tool]].

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

### Login in using the Trucker CLI
```
$ truck login
email>
password>
```

## Install Trucker Command Line Tool
You'll use the `truck` command line tool to deploy your application. You can also use it to check on the health of your application, change settings, and stop and restart your app.

Because `trucker-cli` is a Ruby gem, you will need to have Ruby and RubyGems installed. See the [Installing Ruby](installing-ruby) page for help installing Ruby and RubyGems.

To install `trucker-cli`, simply type the following at your command line:
```
$ gem install trucker-cli
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