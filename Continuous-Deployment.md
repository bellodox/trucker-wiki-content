## Overview

Continuous deployment is part of the continuous delivery paradigm. The basic idea of continuous delivery is to automate the software delivery process as far as possible. This includes automated testing, continuous integration as well as continuous deployment.

* [[How does it work|Continuous-Deployment#How-does-it-work]]
* [[Create a manifest yaml|Continuous-Deployment#Create-a-manifest-yaml]]
* [[Continuous Deployment with Github and Travis CI|Continuous-Deployment#Continuous-Deployment-with-Github-and-Travis-CI]]
* [[Continuous Deployment with Github and Jenkins|Continuous-Deployment#Continuous-Deployment-with-Github-and-Jenkins]]

## How does it work

Once you have set up continuous deployments all you have to care about is pushing your code into your github repository.

Your tests will be executed and succeeding commits will be deployed to your Trucker.io account, automatically.

In order to make this happen only a few steps are necessary.

## Create a manifest yaml

**Travis** - **Jenkins** will later perform unattended deployments for you. Therefore, your app needs to contain some information about the deployment. Cloud Foundry, the technology used by Trucker.io, stores this meta data in a yaml file called `manifest.yml`.

Creating a `manifest.yml` is easy. Just go to your local app directory and invoke

```
truck push
```

This will trigger a first deployment to Trucker.io. The 'trucker-cli' gem will notice that there is no `manifest.yml` and will ask you a standard set of configuration questions such as the desired number and memory size of your app instances, whether and which services to bind to them and most importantly, whether you want to store this information.
Please answer this question with yes as it will create the desired `manifest.yml` file.

Once your `push` was successful, you should be able to access your application using a browser of your choice which means your are ready to setup Travis or Jenkins.

## Continuous Deployment with Github and Travis CI

Go to your local app directory and create a `.travis.yml` file. At the moment, paste the following content. We’ll add some more information, later using the `travis` gem.

```
language: ruby
script: 'RAILS_ENV=test bundle exec rake test'
```

Now your app contains the travis configuration but how should travis know when to pull your code from github and trigger test execution? This is where github hooks come into play.

Basically there are two ways to enable github hooks for travis.

**The hard way:**
Go to travis-ci.org > Accounts > Get your token; Go to github > Your repo > Settings > Hooks > Travis and paste your token there.

**The easy way:**
Go to travis-ci.org > Accounts > Repositories and the activate github travis hook by using the on/off switch for the repository of your choice.

### Setting up travis deployment to Trucker.io

Now it is time to finish our setup by telling travis how to deploy to Trucker.io. Again, there are potentially two ways to achieve this. You could either edit `.travis.yml` or go the easy way and use the `travis` gem.

```
gem install travis
```

Now use the travis command to setup the Trucker.io deployment

```
travis setup cloudfoundry
```

In case you don’t know the Trucker.io target URL use

```
truck target
```

to gather all information required for travis setup. This includes target url, username, the organization and space you are currently using.

As of the time of this writing the Trucker.io target URL is:

```
https://api.trucker.io
```

After the travis command has finished, your `.travis.yml` could look like this:

```
language: ruby
script: 'RAILS_ENV=test bundle exec rake test'
deploy:
  provider: cloudfoundry
  target: https://api.trucker.io
  username: john.doe@example.com
  password:
    secure: your encryped password determined by the travis gem=
  organization: john_doe_example_com
  space: heaven
  on:
    repo: trucker/trucker-travis
```

Don’t forget to commit and push your changes to `.travis.yml` as it will be required in your github repository to take effect.

From now on whenever you commit changes to your github repository, tests will be run and your app is being deployed.

**Try it out!**

Travis will then show log output similar to this:

```
Installing deploy dependencies
...
22 gems installed
dpl.2
Preparing deploy
Setting target to http://api.trucker.io...... OK
target: https://api.trucker.io
Authenticating.. .  ... OK
Switching to organization john_doe_example_com... OK
Switching to space heaven... OK
dpl.3
Deploying application
Using manifest file manifest.yml
Uploading trucker-travis... OK
Stopping trucker-travis... OK
Preparing to start trucker-travis... OK
Checking status of app 'trucker-travis'...
  0 of 1 instances running (1 starting)
  0 of 1 instances running (1 starting)
  1 of 1 instances running (1 running)
Push successful! App 'trucker-travis' available at http://trucker-travis.trucker.io
Logging out... OK
```

This means your are done and good to go! Have fun with continuous deployment.

## Continuous Deployment with Github and Jenkins

First setup your Jenkins as normally.

**Install after build plugin**

Head over to the plugins section en search for the `Hudson Post build task`.
http://wiki.hudson-ci.org/display/HUDSON/Post+build+task

When the plugin is installed your ready to fire custom commands after the build step.

**Update job configuration**

1. Visit your job configuration screen
1. Choose `Post build task` from the 'Add post-build action' dropdown menu
1. Fill the **Log text field** with `0 failures, 0 errors`
1. Now it's time for the script part

**Post build script**

Copy the contents of `~/.truck/tokens.yml` on your local machine. This is needed for later.

```
cat ~/.truck/tokens.yml
```

Fill the script textarea with:

```
#!/bin/bash

[[ -d "${HOME}/.truck" ]] || mkdir -p "${HOME}/.truck"
echo "http://api.trucker.io" >"${HOME}/.truck/target"
cat <<'EOF' >"$HOME/.truck/tokens.yml"
http://api.trucker.io:
  :version: 2
  :token: bearer 
lOnGrAnDomStRiNg
  :refresh_token: 
lOnGrAnDomStRiNg
  :space: ab12c3d4-5678-9efg-hij0-k1234mn567opq
  :organization: ab12c3d4-5678-9efg-hij0-k1234mn567opq
EOF
gem install trucker-cli
truck push
```

Save your new configurations

**Test configuration**

Fire up a new build. Follow the console.

The output should look something like:

```
28 tests, 40 assertions, 0 failures, 0 errors, 0 skips
Performing Post build task...
Match found for :0 failures, 0 errors : True
Logical operation result is TRUE
Running script  : #!/bin/bash

[[ -d "${HOME}/.truck" ]] || mkdir -p "${HOME}/.truck"
echo "http://api.trucker.io" >"${HOME}/.truck/target"
cat <<'EOF' >"$HOME/.truck/tokens.yml"
http://api.trucker.io:
  :version: 2
  :token: bearer 
lOnGrAnDomStRiNg
  :refresh_token: 
lOnGrAnDomStRiNg
  :space: ab12c3d4-5678-9efg-hij0-k1234mn567opq
  :organization: ab12c3d4-5678-9efg-hij0-k1234mn567opq
EOF
gem install trucker-cli
truck push
[trucker-travis] $ /bin/bash /tmp/hudson211628979674008472.sh
Using manifest file manifest.yml

-----> Downloaded app package (56K)
-----> Downloaded app buildpack cache (13M)
-----> Using Ruby version: ruby-1.9.3
-----> Installing dependencies using Bundler version 1.3.2
       Running: bundle install --without development:test --path vendor/bundle --binstubs vendor/bundle/bin --deployment
       ...
       Your bundle is complete! It was installed into ./vendor/bundle
       Cleaning up the bundler cache.
-----> Writing config/database.yml to read from DATABASE_URL
-----> Preparing app for Rails asset pipeline
       Running: rake assets:precompile
       Asset precompilation completed (37.48s)
-----> Rails plugin injection
       Injecting rails_log_stdout
       Injecting rails3_serve_static_assets
-----> Uploading droplet (35M)
Checking status of app 'trucker-travis'...
0 of 1 instances running (1 starting)
1 of 1 instances running (1 running)
Push successful! App 'trucker-travis' available at trucker-travis.trucker.io
POST BUILD TASK : SUCCESS
END OF POST BUILD TASK : 0
Finished: SUCCESS
```

This means your are done and good to go! Have fun with continuous deployment.