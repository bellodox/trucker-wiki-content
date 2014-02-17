### Table of Contents

* [[Local workstation setup|Nginx#local-workstation-setup-2]]
* [[Custom configuration files|Nginx#Custom-configuration-files]]
* [[Deploy your application to Trucker|Nginx#Deploy-your-application-to-trucker-2]]
* [[Example manifest file|Nginx#Example-manifest-file]]

This quickstart will get you going with hosting static HTML sites on Trucker.io using Nginx.

## Local workstation setup<a name="local-workstation-setup-2"></a>

[Install Trucker Command Line Tool](Getting-Started#install-trucker-command-line-tool) on your local workstation. This ensures that you have access to the Trucker command-line client, Foreman, and the Git revision control system.
Once installed, you’ll have access to the trucker command from your command shell. Log in using the email address and password you used when creating your Trucker account:

```
$ truck login
email>
password>
```

## Custom configuration files

You can customise the configuration by adding a `nginx.conf` to your root folder.

If the buildpack detects this file it will be used in place of the built-in `nginx.conf`, and run through the
same erb processor.  An example of the most basic `nginx.conf` (this is the one included in the build pack's `conf` directory):

```
worker_processes 1;
daemon off;

error_log <%= ENV["APP_ROOT"] %>/nginx/logs/error.log;
events { worker_connections 1024; }

http {
  log_format trucker '$http_x_forwarded_for - $http_referer - [$time_local] "$request" $status $body_bytes_sent';
  access_log <%= ENV["APP_ROOT"] %>/nginx/logs/access.log cloud foundry;
  default_type application/octet-stream;
  include mime.types;
  sendfile on;
  gzip on;
  tcp_nopush on;
  keepalive_timeout 30;

  server {
    listen <%= ENV["PORT"] %>;
    server_name localhost;

    location ~ /\.ht { deny  all; }
    location / {
      root <%= ENV["APP_ROOT"] %>/public;
      index index.html index.htm Default.htm;
    }
  }
}
```

## Deploy your application to Trucker<a name="deploy-your-application-to-trucker-2"></a>

**Push the application to Trucker.io**

It's important to include the `--buildpack` after the `push` command, to use static HTML hosting on Trucker.io using Nginx.

First perform a push command and enter your desired application name.

```
$ truck push --buildpack https://github.com/trucker/nginx-buildpack.git
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

**Deployment**

You app will now be deployed, the output should look something like this:

```
Creating trucker-test... OK

Binding trucker-test.trucker.io to trucker-test... OK
Uploading trucker-test... OK
Preparing to start trucker-test... OK
-----> Downloaded app package (360K)
Initialized empty Git repository in /tmp/buildpacks/nginx-buildpack.git/.git/
-----> Doing work with nginx-1.4.1 son.
-----> Ensuring manifest.yml & stackato.yml aren't availble in public/
-----> Uploading droplet (1.3M)
Checking status of app 'trucker-test'....
  1 of 1 instances running (1 running)
Push successful! App 'trucker-test' available at trucker-test.trucker.io
```

You application is now available at http://trucker-test.trucker.io

## Example manifest file

This how your `manifest.yml` could look like.

```
---
applications:
- name: trucker-test
  memory: 128M
  instances: 1
  host: trucker-test
  domain: trucker.io
  path: .
  buildpack: https://github.com/trucker/nginx-buildpack.git

```