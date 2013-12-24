# Rails 3.x

The aim of this guide is to walk you through the deployment of a [Ruby on Rails](http://rubyonrails.org/) application. It is provided as an addendum to the [[Deploy your Application]] guide.

### Prerequisites

Deploying a Rails app on Trucker requires a bit of basic knowledge on:

* Ruby, Rubygems, Bundler, and of course Rails 3
* Git SCM

Additionally, you should have a Trucker account, the trucker command line client installed on your machine, and logged in the Trucker service. If not, have a look at our [[getting started]] section.

## Deploy your application to Trucker

**Push the application to Trucker.io**

First perform a push command and enter your desired application name.

```bash
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

Create services for application?> y

1: rds-mysql n/a, via aws
2: redis 2.6
3: user-provided , via
What kind?> 1

Name?> rds-mysql-aa0d6

1: 100mb: Shared service, 100MB storage, 10 connections
Which plan?> 1

Creating service rds-mysql-aa0d6... OK
Binding rds-mysql-aa0d6 to trucker-rails... OK
Create another service?> n

Bind other services to application?> n

Save configuration?> y

Saving to manifest.yml... OK
Uploading trucker-rails
```


**Deployment**

You app will now be deployed, the output should look something like this:

```bash
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

To use the Rails console with your database service, you will have to create a tunnel to the service, and choose 'none' when it asks you which client to start:

```bash
$ truck tunnel rds-mysql-aa0d6

Opening tunnel on port 10000... OK

Service connection info:
  username : a1bc23d4567890
  password : abcde1fg
  name     : ab_c123d45de67f890
  jdbcUrl  : jdbc:mysql://a1bc23d4567890:abcde1fg@ccdb.cb1a3tdjjo2d.eu-west-1.rds.amazonaws.com:3306/ab_c123d45de67f890
  uri      : mysql://a1bc23d4567890:abcde1fg@ccdb.cb1a3tdjjo2d.eu-west-1.rds.amazonaws.com:3306/ab_c123d45de67f890?reconnect=true

Press Ctrl-C to exit...
```

Next, create another database section in your `config/database.yml` file with the service connection info in the truck tunnel output:

```yaml
proxied-trucker: 
  adapter: mysql2 
  database : ab_c123d45de67f890
  username : a1bc23d4567890
  password : abcde1fg
  port: 10000 
  host: 127.0.0.1
```

Finally, in a another terminal, run `rails console`, passing in the database environment you created:

```bash
$ RAILS_ENV=proxied-trucker bundle exec rails console
```

That's it, you now have a Rails console proxied to your Trucker database service!

### rake

Using the proxied environment you can now also perform `rake` commands.

```bash
$ RAILS_ENV=proxied-trucker bundle exec rake [command]
```

## Database migrations

### Rake command
One option is to setup the proxied `RAILS_ENV` as mentioned above and perform a rake command.

```bash
$ RAILS_ENV=proxied-trucker bundle exec rake db:migrate
```

### Initializer file

It is possible to include your migration execution within a rails initializer when your application starts.

To follow this approach you have to create a new initializer file within the `config/initializers` directory. Let's call it `run_migrations.rb`.

The file should contain the following line of code for executing the database migrations: 

```bash
ActiveRecord::Migrator.migrate(Rails.root.join("db/migrate"), nil)
```

And that's it. Next time migrations will be run automatically when you execute `truck push`.

## View the logs

To show the application log use the following command:

```bash
$ truck logs
```

To tail your application log use:

```bash
$ truck tail [app-name]
```

## Development mode

You are able to force your application to use the development environment when running on Trucker.io. You can achieve this by setting the `RAILS_ENV` environment variable for your application:

```bash
truck set-env [application-name] RAILS_ENV=development
```

This can be very handy when debugging application or deployment errors.

When you are finished with debugging your application you can reset the environment to production by deleting the `RAILS_ENV` variable again:

```bash
truck unset-env [application-name] RAILS_ENV
```