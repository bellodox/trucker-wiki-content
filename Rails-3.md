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