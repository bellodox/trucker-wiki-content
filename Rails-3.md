# Rails 3

### Table of Contents

* [[Prerequisites|Rails-3#prerequisites]]
* [[Local workstation setup|Rails-3#local-workstation-setup]]
* [[Write your app|Rails-3#write-your-app]]
* [[Store your app in Git|Rails-3#store-your-app-in-git]]
* [[Deploy your application to Trucker|Rails-3#deploy-your-application-to-trucker]]
* [[Visit your application|Rails-3#visit-your-application]]
* [[Dyno sleeping and scaling|Rails-3#dyno-sleeping-and-scaling]]
* [[View the logs|Rails-3#view-the-logs]]
* [[Console|Rails-3#console]]
* [[Rake and Database Migrations|Rails-3#rake-and-database-migrations]]
* [[Webserver|Rails-3#webserver]]
* [[Troubleshooting|Rails-3#troubleshooting]]
* [[Next steps|Rails-3#next-steps]]

This quickstart will get you going with Rails 3, deployed to Trucker. The latest version of Rails is Rails 4. If you’re starting a new app, you’ll probably want to use Getting Started with Rails 4. For Sinatra or other Ruby apps, please see Getting Started with Ruby on Trucker.

### Prerequisites

Basic Ruby/Rails knowledge, including an installed version of Ruby 2.0.0, Rubygems, Bundler, and Rails 3.
Basic Git knowledge
Your application must run on Ruby (MRI) 2.0.0.
A Trucker user account. Signup is free and instant.

### Local workstation setup

Install trucker Command Line Tool on your local workstation. This ensures that you have access to the Trucker command-line client, Foreman, and the Git revision control system.
Once installed, you’ll have access to the trucker command from your command shell. Log in using the email address and password you used when creating your Trucker account:

```
$ trucker login
email>
password>
```

We highly recommend using PostgreSQL during development. Maintaining parity between your development and deployment environments prevents subtle bugs from being introduced because of differences between your environments.

Trucker provides you a PostgreSQL database for our app, so we’ll be using PostgreSQL as our local database as well. You will need to set this up locally.

## Write your app

### New App

You may be starting from an existing app. If not, a vanilla Rails 3 app will serve as a suitable sample app:

```
$ rails new myapp --database=postgresql
$ cd myapp
```

### Existing App

If you’re upgrading an existing app, make sure you’re using PostgreSQL instead of SQLite3. Edit your Gemfile and change this line:

```
gem 'sqlite3'
```

To this:

```
gem 'pg'
```

In addition to using the `pg` gem, you'll also need to ensure the `config/database.yml` is using the `postgresql` adapter and not `sqlite3`. Your `config/database.yml` file should look something like this:

```
development:
  adapter: postgresql
  encoding: unicode
  database: myapp_development
  pool: 5
  username: myapp
  password:
test:
  adapter: postgresql
  encoding: unicode
  database: myapp_test
  pool: 5
  username: myapp
  password:
production:
  adapter: postgresql
  encoding: unicode
  database: myapp_production
  pool: 5
  username: myapp
  password:
```

Make sure to update the username and password for your database.

And re-install your dependencies (to generate a new `Gemfile.lock`):

```
$ bundle install
```

### Rails Plugins

In order to get the most out of the Trucker platform, include the `rails_12factor` gem. This configures things like logging in your app to work on the platform.

```
gem 'rails_12factor', group: :production
```

You’ll need to update your dependencies using bundler.

```
$ bundle install
```

You can read more on this on our Ruby Support document.

### Setting up the Asset Pipeline

Rails 3.1 introduced the Asset Pipeline to concatenate and minify or compress JavaScript and CSS assets. Trucker has a step in the build process to precompile your assets into your slug, so they’re readily available. To speed up asset precompiles, it’s recommended that you tell Rails to only partially load your app. Trucker also, does not provide the whole app environment to the build process, so this is required. In your `config/application.rb` set the following:

```
config.assets.initialize_on_precompile = false
```

There are several options for invoking the Rails 3.1+ asset pipeline when deploying to Trucker. For full details please see the Rails 3.1+ Asset Pipeline on Trucker Cedar article.

### Specify your Ruby Version

Since you’ll want development / produciton parity, you’ll want to specify the same version of Ruby locally that you have in production. We’ll be using the ruby DSL introduced by Bundler. In your Gemfile add this to the bottom:

```
ruby '2.0.0'
```

You can read more about specifying your Ruby Version.

## Store your app in Git

```
$ git init
$ git add .
$ git commit -m "init"
```

## Deploy your application to Trucker

### Create the app on Trucker:

```
$ trucker create
Creating severe-mountain-793... done, stack is cedar
http://severe-mountain-793.truckerapp.com/ | git@trucker.com:severe-mountain-793.git
Git remote trucker added
```

### Deploy your code:

```
$ git push trucker master
Counting objects: 67, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (52/52), done.
Writing objects: 100% (67/67), 86.33 KiB, done.
Total 67 (delta 5), reused 0 (delta 0)

-----> Ruby/Rails app detected
-----> Using Ruby version: ruby-2.0.0
-----> Installing dependencies using Bundler version 1.3.2
       Running: bundle install --without development:test --path vendor/bundle --binstubs vendor/bundle/bin --deployment
       Fetching gem metadata from https://rubygems.org/..........
       Fetching gem metadata from https://rubygems.org/..
       Installing rake (10.1.0)
       ...
       Installing uglifier (2.2.1)
       Your bundle is complete! It was installed into ./vendor/bundle
       Post-install message from rdoc:
       Depending on your version of ruby, you may need to install ruby rdoc/ri data:
       <= 1.8.6 : unsupported
       = 1.8.7 : gem install rdoc-data; rdoc-data --install
       = 1.9.1 : gem install rdoc-data; rdoc-data --install
       >= 1.9.2 : nothing to do! Yay!
       Cleaning up the bundler cache.
-----> Writing config/database.yml to read from DATABASE_URL
-----> Preparing app for Rails asset pipeline  
       Running: rake assets:precompile
       Compiled jquery.js  (9ms)  (pid 735)
       Compiled jquery_ujs.js  (0ms)  (pid 735)
       Compiled application.js  (32ms)  (pid 735)
       Compiled application.css  (2ms)  (pid 735)
       Compiled jquery.js  (5ms)  (pid 735)
       Compiled jquery_ujs.js  (0ms)  (pid 735)
       Compiled application.js  (16ms)  (pid 735)
       Compiled application.css  (1ms)  (pid 735)
       Asset precompilation completed (19.16s)
-----> Discovering process types
       Procfile declares types -> (none)
       Default types for Rails -> console, rake, web, worker
-----> Compiled slug size: 34.0MB
-----> Launching... done, v5
       http://severe-mountain-793.truckerapp.com deployed to Trucker

To git@trucker.com:severe-mountain-793.git
 * [new branch]      master -> master
```

## Visit your application

You’ve deployed your code to Trucker. You can now instruct Trucker to execute a process type. Trucker does this by running the associated command in a dyno - a lightweight container which is the basic unit of composition on Trucker.

Let’s ensure we have one dyno running the web process type:

```
$ trucker ps:scale web=1
```

You can check the state of the app’s dynos. The trucker ps command lists the running dynos of your application:

```
$ trucker ps
=== web: `bundle exec rails server -p $PORT`
web.1: up for 5s
```

Here, one dyno is running.

We can now visit the app in our browser with trucker open.

```
$ trucker open
Opening severe-mountain-793... done
```