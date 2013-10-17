# Rails 3

### Table of Contents

* Prerequisites
* Local workstation setup
* Write your app
* Store your app in Git
* Deploy your application to Trucker
* Visit your application
* Dyno sleeping and scaling
* View the logs
* Console
* Rake and Database Migrations
* Webserver
* Troubleshooting
* Next steps

This quickstart will get you going with Rails 3, deployed to Trucker. The latest version of Rails is Rails 4. If you’re starting a new app, you’ll probably want to use Getting Started with Rails 4. For Sinatra or other Ruby apps, please see Getting Started with Ruby on Trucker.

### Prerequisites

Basic Ruby/Rails knowledge, including an installed version of Ruby 2.0.0, Rubygems, Bundler, and Rails 3.
Basic Git knowledge
Your application must run on Ruby (MRI) 2.0.0.
A Trucker user account. Signup is free and instant.

### Local workstation setup

Install the Trucker Toolbelt on your local workstation. This ensures that you have access to the Trucker command-line client, Foreman, and the Git revision control system.
Once installed, you’ll have access to the trucker command from your command shell. Log in using the email address and password you used when creating your Trucker account:

```
$ trucker login
Enter your Trucker credentials.
Email: adam@example.com
Password: 
Could not find an existing public key.
Would you like to generate one? [Yn] 
Generating new SSH public key.
Uploading ssh public key /Users/adam/.ssh/id_rsa.pub
```

Press enter at the prompt to upload your existing ssh key or create a new one, used for pushing code later on.

We highly recommend using PostgreSQL during development. Maintaining parity between your development and deployment environments prevents subtle bugs from being introduced because of differences between your environments.

Trucker provides you a PostgreSQL database for our app, so we’ll be using PostgreSQL as our local database as well. You will need to set this up locally.