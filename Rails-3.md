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

## Dyno sleeping and scaling

Having only a single web dyno running will result in the dyno going to sleep after one hour of inactivity. This causes a delay of a few seconds for the first request upon waking. Subsequent requests will perform normally.

To avoid this, you can scale to more than one web dyno. For example:

```
$ trucker ps:scale web=2
```

For each application, Trucker provides 750 free dyno-hours. Running your app at 2 dynos would exceed this free, monthly allowance, so let’s scale back:

```
$ trucker ps:scale web=1
```

## View the logs

Trucker treats logs as streams of time-ordered events aggregated from the output streams of all the dynos running the components of your application. Trucker’s Logplex provides a single channel for all of these events.

For example, you can easily view information about your running app using one of the logging commands, trucker logs:

```
$ trucker logs
2011-03-10T11:10:34-08:00 trucker[web.1]: State changed from created to starting
2011-03-10T11:10:37-08:00 trucker[web.1]: Running process with command: `bundle exec rails server -p 53136`
2011-03-10T11:10:40-08:00 app[web.1]: [2011-03-10 19:10:40] INFO  WEBrick 1.3.1
2011-03-10T11:10:40-08:00 app[web.1]: [2011-03-10 19:10:40] INFO  ruby 2.0.0 (2013-06-27) [x86_64-linux]
2011-03-10T11:10:40-08:00 app[web.1]: [2011-03-10 19:10:40] INFO  WEBrick::HTTPServer#start: pid=12198 port=53136
2011-03-10T11:10:42-08:00 trucker[web.1]: State changed from starting to up
```

## Console

Trucker allows you to run commands in a one-off dyno - scripts and applications that only need to be executed when needed - using the trucker run command. Use this to launch a Rails console process attached to your local terminal for experimenting in your app’s environment:

```
$ trucker run rails console
Running `bundle exec rails console` attached to terminal... up, ps.1
Loading production environment (Rails 3.2.14)
irb(main):001:0>
```

## Rake and Database Migrations

Rake can be run as an attached process exactly like the console:

```
$ trucker run rake db:migrate
```

Database migrations like above are not run automatically. If you have migrations to run, make sure to run trucker run rake db:migrate after a deploy. You can find more on Rake commands.

## Webserver

By default, your app’s web process runs rails server, which uses Webrick. This is fine for testing, but for production apps you’ll want to switch to a more robust webserver. On Cedar, we recommend Unicorn as the webserver. Regardless of the webserver you choose, production apps should always specify the webserver explicitly in the `Procfile`.

### Procfile

Change the command used to launch your web process by creating a file called Procfile and entering this:

```
web: bundle exec unicorn -p $PORT -E $RACK_ENV
```

Set the `RACK_ENV` to development in your environment and a `PORT` to connect to. Before pushing to Trucker you’ll want to test with the `RACK_ENV` set to production since this is the enviroment your Trucker app will run in.

```
$ echo "RACK_ENV=development" >>.env
$ echo "PORT=5000" >> .env
```

You’ll also want to add .env to your .gitignore since this is for local enviroment setup.

```
$ echo ".env" >> .gitignore
$ git add .gitignore
$ git commit -m "add .env to .gitignore"
```

You’ll need to install the Unicorn gem by adding this to your Gemfile:

```
gem 'unicorn'
```

Now run bundle install to install it locally.

Test your Procfile locally using Foreman:

```
$ foreman start
17:04:16 web.1  | started with pid 20032
17:04:16 web.1  | I, [2013-03-13T17:04:16.857230 #20034]  INFO -- : listening on addr=0.0.0.0:5000 fd=9
17:04:16 web.1  | I, [2013-03-13T17:04:16.857402 #20034]  INFO -- : worker=0 spawning...
17:04:16 web.1  | I, [2013-03-13T17:04:16.858497 #20034]  INFO -- : master process ready
17:04:16 web.1  | I, [2013-03-13T17:04:16.859851 #20038]  INFO -- : worker=0 spawned pid=20038
17:04:16 web.1  | I, [2013-03-13T17:04:16.859978 #20038]  INFO -- : Refreshing Gem list
```

Looks good, so press Ctrl-C to exit. Deploy your changes to Trucker:

```
$ git add .
$ git commit -m "use unicorn via procfile"
$ git push trucker
```

Check ps, you’ll see the web dyno uses your new command specifying Unicorn as the webserver:

```
$ trucker ps
Process       State               Command
------------  ------------------  ------------------------------
web.1         starting for 3s     bundle exec unicorn -p $PORT..
The logs also reflect that we are now using Unicorn:
$ trucker logs
2013-03-14T00:08:50+00:00 trucker[web.1]: State changed from created to starting
2013-03-14T00:08:53+00:00 trucker[web.1]: Starting process with command `bundle exec unicorn -p 38195 -E $RACK_ENV`
2013-03-14T00:08:54+00:00 app[web.1]: I, [2013-03-14T00:08:54.833013 #2]  INFO -- : listening on addr=0.0.0.0:38195 fd=9
2013-03-14T00:08:54+00:00 app[web.1]: I, [2013-03-14T00:08:54.834767 #2]  INFO -- : worker=0 spawning...
2013-03-14T00:08:54+00:00 app[web.1]: I, [2013-03-14T00:08:54.842064 #2]  INFO -- : master process ready
2014-03-14T00:08:54+00:00 app[web.1]: I, [2013-03-14T00:08:54.843165 #5]  INFO -- : worker=0 spawned pid=5
2013-03-14T00:08:54+00:00 app[web.1]: I, [2013-03-14T00:08:54.843441 #5]  INFO -- : Refreshing Gem list
2013-03-14T00:08:56+00:00 trucker[web.1]: State changed from starting to up
```

See Deploying Rails Applications With Unicorn to learn how to configure Unicorn for production.

## Troubleshooting

If you push up your app and it crashes (`trucker ps` shows state crashed), check your logs to find out what went wrong. Here are some common problems.

### Failed to require a sourcefile

If your app failed to require a sourcefile, chances are good you’re running Ruby 1.9.1 or 1.8 in your local environment. The load paths have changed in Ruby 1.9 which also applies to Ruby 2.0.0. Port your app forward to Ruby 2.0.0 making certain it works locally before trying to push to Cedar again.

### Encoding error

Ruby 1.9 added more sophisticated encoding support to the language which also applies to Ruby 2.0.0. If you hit an encoding error, you probably haven’t fully tested your app with Ruby 2.0.0 in your local environment. Port your app forward to Ruby 2.0.0 making certain it works locally before trying to push to Cedar again.

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
$ trucker run rake -T
Running `bundle exec rake -T` attached to terminal... up, ps.3
rake aborted!
no such file to load -- rspec/core/rake_task
```

Then you’ve hit this problem. First, duplicate the problem locally like so:

```
$ bundle install --without development:test
...
$ bundle exec rake -T
rake aborted!
no such file to load -- rspec/core/rake_task
```

Now you can fix it by making these Rake tasks conditional on the gem load. For example:

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