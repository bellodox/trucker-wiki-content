# Rails 3

### Table of Contents

* [[Prerequisites|Rails-3#prerequisites]]
* [[Local workstation setup|Rails-3#local-workstation-setup]]
* [[Write your app|Rails-3#write-your-app]]
* [[Store your app in Git|Rails-3#store-your-app-in-git]]
* [[Deploy your application to Trucker|Rails-3#deploy-your-application-to-trucker]]
* [[Rails Console and Rake|Rails-3#rails-console-and-rake]]
* [[Database migrations|Rails-3#database-migrations]]
* [[View the logs|Rails-3#view-the-logs]]
* [[Development mode|Rails-3#development-mode]]
* [[Troubleshooting|Rails-3#rails-troubleshooting]]

This quickstart will get you going with Rails 3, deployed to Trucker. The latest version of Rails is Rails 4. If you’re starting a new app, you’ll probably want to use Getting Started with Rails 4. For Sinatra or other Ruby apps, please see Getting Started with Ruby on Trucker.

### Prerequisites

Basic Ruby/Rails knowledge, including an installed version of Ruby 2.0.0, Rubygems, Bundler, and Rails 3.
Basic Git knowledge
Your application must run on Ruby (MRI) 2.0.0.
A Trucker user account. Signup is free and instant.

### Local workstation setup

[Install Trucker Command Line Tool](Getting-Started#install-trucker-command-line-tool) on your local workstation. This ensures that you have access to the Trucker command-line client, Foreman, and the Git revision control system.
Once installed, you’ll have access to the trucker command from your command shell. Log in using the email address and password you used when creating your Trucker account:

```
$ truck login
email>
password>
```

We highly recommend using Mysql during development. Maintaining parity between your development and deployment environments prevents subtle bugs from being introduced because of differences between your environments.

## Write your app

### New App

You may be starting from an existing app. If not, a vanilla Rails 3 app will serve as a suitable sample app:

```
$ rails new trucker-test
$ cd trucker-test
```

### Existing App

Ruby on Rails app deployments on Trucker automatically recognize MySQL.

Make sure to use the correct version of the MySQL2 gem in your Gemfile:

**Rails 3.0**

```
# If you use a different database in development, hide it from Trucker
group :development do
  gem 'sqlite3'
end
# Rails 3.0 requires version less than 0.3 of mysql2 gem
group :production do
  gem 'mysql2', '< 0.3'
end
```

**Rails 3.1.x**

```
# If you use a different database in development, hide it from Trucker
group :development do
  gem 'sqlite3'
end
# Rails 3.1 can use the latest mysql2 gem.
group :production do
  gem 'mysql2'
end
```

In addition to using the `mysql2` gem, you'll also need to ensure the `config/database.yml` is using the `mysql2` adapter and not `sqlite3`. Your `config/database.yml` file should look something like this:

```
development:
  adapter: sqlite3
  database: db/development.sqlite3
  pool: 5
  timeout: 5000

test:
  adapter: sqlite3
  database: db/test.sqlite3
  pool: 5
  timeout: 5000

production:
  adapter: mysql2
  encoding: utf8
  database: 
  pool: 5
  username: 
  password:
```

And re-install your dependencies (to generate a new `Gemfile.lock`):

```
$ bundle install
```

### Setting up the Asset Pipeline

Rails 3.1 introduced the Asset Pipeline to concatenate and minify or compress JavaScript and CSS assets. Trucker has a step in the build process to precompile your assets into your slug, so they’re readily available. To speed up asset precompiles, it’s recommended that you tell Rails to only partially load your app. Trucker also, does not provide the whole app environment to the build process, so this is required. In your `config/application.rb` set the following:

```
config.assets.initialize_on_precompile = false
```

**Disable Rails's static asset server**

In `config/environments/production.rb`, change

```
config.server_static_assets = false
```

to

```
config.server_static_assets = true
```

**Compiling**

Pre-compile your asset pipeline:

```
$ bundle exec rake assets:precompile
```

### Specify your Ruby Version

Since you’ll want development / produciton parity, you’ll want to specify the same version of Ruby locally that you have in production. We’ll be using the ruby DSL introduced by Bundler. In your Gemfile add this to the bottom:

```
ruby '1.9.3'
```

or

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

**Push the application to Trucker.io**

First perform a push command and enter your desired application name.

```
$ truck push
Name> trucker-test

Instances> 1

1: 128M
2: 256M
3: 512M
4: 1G
Memory Limit> 256M

Creating trucker-test... OK

1: trucker-test
2: none
Subdomain> trucker-test

1: trucker.io
2: none
Domain> trucker.io

Creating route trucker-test.trucker.io... OK
Binding trucker-test.trucker.io to trucker-test... OK
```

Time to add a mysql service

```
Create services for application?> y

1: blazemeter n/a, via blazemeter
2: cleardb n/a, via cleardb
3: cloudamqp n/a, via cloudamqp
4: elephantsql n/a, via elephantsql

What kind?> 2

Name?> cleardb-505d6

Creating service cleardb-505d6... OK
Binding cleardb-505d6 to trucker-test... OK
Create another service?> n
```

**Deployment**

You app will now be deployed, the output should look something like this:

```
Uploading trucker-test... OK
Preparing to start trucker-test... OK
-----> Downloaded app package (40K)
-----> Using Ruby version: ruby-1.9.3
-----> Installing dependencies using Bundler version 1.3.2
       Running: bundle install --without development:test --path vendor/bundle --binstubs vendor/bundle/bin --deployment
       Fetching gem metadata from https://rubygems.org/..........
       Fetching gem metadata from https://rubygems.org/..
       Installing rake (10.1.0)
       ...
       Your bundle is complete! It was installed into ./vendor/bundle
-----> Writing config/database.yml to read from DATABASE_URL
-----> Preparing app for Rails asset pipeline
       Running: rake assets:precompile
       Asset precompilation completed (9.59s)
-----> Rails plugin injection
       Injecting rails_log_stdout
       Injecting rails3_serve_static_assets
-----> Uploading droplet (41M)
Checking status of app 'trucker-test'...
  1 of 1 instances running (1 running)
Push successful! App 'trucker-test' available at http://trucker-test.trucker.io
```

You application is now available at http://trucker-test.trucker.io

## Rails Console and Rake

### rails c

To use the Rails console with your database service, tunnel into the service, and choose 'none' when it asks you which client to start:

```
$ truck tunnel cleardb-505d6

Opening tunnel on port 10000... OK

Service connection info:
  username : a1bc23d4567890
  password : abcde1fg
  name     : ab_c123d45de67f890
  jdbcUrl  : jdbc:mysql://a1bc23d4567890:abcde1fg@us-cdbr-east-04.cleardb.com:3306/ab_c123d45de67f890
  uri      : mysql://a1bc23d4567890:abcde1fg@us-cdbr-east-04.cleardb.com:3306/ab_c123d45de67f890?reconnect=true


Open another shell to run command-line clients or
use a UI tool to connect using the displayed information.
Press Ctrl-C to exit...
```

Next, create another database section in your `config/database.yml` file with the service connection info in the af tunnel output:

```
proxied-trucker: 
  adapter: mysql2 
  database : ab_c123d45de67f890
  username : a1bc23d4567890
  password : abcde1fg
  port: 10000 
  host: 127.0.0.1
```

Finally, in a another terminal, run `rails console`, passing in the database environment you created:

```
$ RAILS_ENV=proxied-trucker bundle exec rails console
```

That's it, you now have a Rails console proxied to your Trucker database service!

### rake

Using the proxied environment you can now also perform `rake` commands.

```
$ RAILS_ENV=proxied-trucker bundle exec rake [command]
```

## Database migrations

### Rake command
One option is to setup the proxied `RAILS_ENV` as mentioned above and perform a rake command.

```
$ RAILS_ENV=proxied-trucker bundle exec rake db:migrate
```

### Initializer file

It is possible to include your migration execution within a rails initializer when your application starts.

To follow this approach you have to create a new initializer file within the `config/initializers` directory. Let's call it `run_migrations.rb`.

The file should contain the following line of code for executing the database migrations: 

```
ActiveRecord::Migrator.migrate(Rails.root.join("db/migrate"), nil)
```

And that's it. Next time migrations will be run automatically when you execute `truck push`.

## View the logs

To show the application log use the following command:

```
$ truck logs
```

To tail your application log use:

```
$ truck tail [app-name]
```

## Development mode

You are able to force your application to use the development environment when running on Trucker.io. You can achieve this by setting the `RAILS_ENV` environment variable for your application:

```
truck set-env [application-name] RAILS_ENV=development
```

This can be very handy when debugging application or deployment errors.

When you are finished with debugging your application you can reset the environment to production by deleting the `RAILS_ENV` variable again:

```
truck unset-env [application-name] RAILS_ENV
```

## Rails Troubleshooting

### Missing a gem

If your app crashes due to missing a gem, you may have it installed locally but not specified in your `Gemfile`. You must isolate all local testing using `bundle exec`. For example, don’t run `ruby web.rb`, run `bundle exec ruby web.rb`. Don’t run `rake db:migrate`, run `bundle exec rake db:migrate`.

Another approach is to create a blank RVM gemset to be absolutely sure you’re not touching any system-installed gems:

```
$ rvm gemset create myapp
$ rvm gemset use myapp
```

### Runtime dependencies on development/test gems

If you’re still missing a gem when you deploy, check your Bundler groups. Trucker builds your app without the `development` or `test` groups, and if your app depends on a gem from one of these groups to run, you should move it out of the group.

One common example using the RSpec tasks in your `Rakefile`. If you see this in your Trucker deploy:

```
$ bundle install --without development:test
...
$ bundle exec rake -T
rake aborted!
no such file to load -- rspec/core/rake_task
```

Then you’ve hit this problem.
You can fix it by making these Rake tasks conditional on the gem load. For example:

### Rakefile

```
begin
  require "rspec/core/rake_task"

  desc "Run all examples"
  RSpec::Core::RakeTask.new(:spec) do |t|
    t.rspec_opts = %w[--color]
    t.pattern = 'spec/**/*_spec.rb'
  end
rescue LoadError
end
```

Confirm it works locally, then push to Trucker.