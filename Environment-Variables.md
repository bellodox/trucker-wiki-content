Environment variables are the means by which the Trucker runtime communicates with a deployed application about its environment. This page describes the environment variables that Droplet Execution Agents (DEAs) and buildpacks set for applications.

## View Environment Variable Values
The sections below describe methods of viewing the values of Trucker environment variables.

### View Environment Variables using CLI

The truck command line interface provides two commands that can return environment variables.

```
$ truck files APP_NAME_HERE logs/env.log
```

### Access Environment Variables Programmatically

The sections below show how to access an environment variable from a program. These examples obtain the `VCAP_SERVICES` environment variable

### Java

```
System.getenv("VCAP_SERVICES");
```

### Ruby

```
ENV['VCAP_SERVICES']
```

### Node.js

```
process.env.VCAP_SERVICES
```

## Variables Defined by the DEA

The subsections that follow describe the environment variables set by a DEA for an application at staging time.

### HOME
Root folder for the deployed application.  

`HOME=/home/vcap/app`

### MEMORY_LIMIT
The maximum amount of memory that each instance of the application can consume. This value is set as a result of the value you specify in an application manifest, or at the command line when pushing an application.   

If an instance goes over the maximum limit, it will be restarted. If it has to be restarted too often, it will be terminated. 

`MEMORY_LIMIT=512m`

### PORT
The port on the DEA for communication with the application. The DEA allocates a port to the application during staging. For this reason, code that obtains or uses the application port should reference it using `PORT`. 

`PORT=61857`

### PWD
Identifies the present working directory, where the buildpack that processed the application ran.

`PWD=/home/vcap`

### TMPDIR
Directory location where temporary and staging files are stored.

`TMPDIR=/home/vcap/tmp`

### USER
The user account under which the DEA runs.

`USER=vcap`

### VCAP_APP_HOST

The IP address of the DEA host.

`VCAP_APP_HOST=0.0.0.0`

### VCAP_APPLICATION

This variable contains useful information about a deployed application. Results are retured in JSON format. The table below lists the attributes that are returned.


|Attribute|Description |
| --------- | --------- | 
|application_users, users | |
|instance_id  |GUID that identifies the application. |
|instance_index |Index number, relative to the DEA, of the instance. | 
|application_version, version |GUID that identifies a version of the application that was pushed. Each time an application is pushed, this value is updated. | 
|application_name, name |The name assigned to the application when it was pushed. | 
|application_uris |The URI(s) assigned to the application.   | 
|started_at, start |The last time the application was started. | 
|started\_at\_timestamp |Timestamp for the last time the applicaton was started. | 
|host |IP address of the application instance. | 
|port |Port of the application instance. | 
|limits  |The memory, disk, and number of files permitted to the instance. Memory and disk limits are supplied when the application is deployed, either on the command line or in the application manifest. The number of files allowed is operator-defined. | 
|state_timestamp |The timestamp for the time at which the application achieved its current state.| 

```
VCAP_APPLICATION={"todo"}`
```

### VCAP_APP_PORT

Equivalent to the PORT variable, defined above.

### VCAP_CONSOLE_IP

This is not yet implemented in V2.

The IP address upon which application users can access the Rails console.

`VCAP_CONSOLE_IP=0.0.0.0`

### VCAP_CONSOLE_PORT
This is not yet implemented in V2.

The port (on the network interface specified by `VCAP_CONSOLE_IP`) upon which application users can access the Rails console.

`VCAP_CONSOLE_PORT=61858` 

### VCAP_SERVICES

For most service types, Trucker will add connection details to the `VCAP_SERVICES` environment variable when you bind the service to the application.

The results are returned as a JSON document that contains an object for each service type of which one or more instances are bound to the application. The service type object contains a child object for each service instance of that type that is bound to the application. The attributes for a service instance vary somewhat by service type. The attributes that describe a bound service are defined in the table below.  Note that not all attributes apply to all service types.  


|Attribute|Description |
| --------- | --------- | 
|service name-version|A service type is identified by the service name and service version (if there is no version attribute, the string "n/a" is used), separated by a dash character, for example "cleardb-n/a". |
|name|The name assigned to the service instance when it was created. |
|label|Takes the same value as service name-version. |
|plan|The provider plan selected when the service was created. |
|name|The name of the database running on the service provider's server; appears in the "credentials" object for a cleardb instance.  |
|host |The host  for connecting to the service; appears in the "credentials" object for a cleardb, rediscloud, or sendgrid instance.|
|port |Port for connecting to the service; appears in the "credentials" object for a cleardb or rediscloud instance.  |
|username |Username for connecting to the service; appears in the "credentials" object for a cleardb or sendgrid instance.  |
|password |Password for connecting to the service; appears in the "credentials" object for a cleardb, cloudamp, or sendgrid instance.  |
|uri  |URI of the service, appears in the "credentials" object for a cleardb, rediscloud, elephantsql, or mongolab instance.  |
|jdbcUrl|The JDBC URL for the database connection; appears in the "credentials" object for a cleardb instance. |


The example below contains the parsed JSON for the VCAP_SERVICE variable for a bound instance of each service type available in the Trucker Services Marketplace. 

```
VCAP_SERVICES={} # todo
```

## Variables Defined by Java Buildpack

The subsections that follow describe the environment variables set by the Java Buildpack for an application at staging time.

### JAVA_HOME

The location of JAVA on the container running the application.

`JAVA_HOME=/home/vcap/app/.jdk`

### JAVA_OPTS

The Java options to use when running the application. All values are used without modification when invoking the JVM. Can be configured in the Java buildpack’s `/config/javaopts.yml` file.

`JAVA_OPTS=-Xmx512m -Xms512m -Dhttp.port=61857`

### JAVA_TOOL_OPTIONS

This environment variable defines Java options that are required to enable the Java buildpack to auto-configure services for a Java application that uses the Lift framework.
 
`JAVA_TOOL_OPTIONS=-Drun.mode=production`

## Variables Defined by Ruby Buildpack

The subsections that follow describe the environment variables set by the Ruby buildpack for an application at staging time.

### BUNDLE_BIN_PATH

Location where Bundler installs binaries.

`BUNDLE_BIN_PATH:/home/vcap/app/vendor/bundle/ruby/1.9.1/gems/bundler-1.3.2/bin/bundle`

### BUNDLE_GEMFILE

Path to application’s gemfile. 
 
`BUNDLE_GEMFILE:/home/vcap/app/Gemfile`

### BUNDLE_WITHOUT

The `BUNDLE_WITHOUT` environment variable causes Trucker to skip installation of gems in excluded groups. `BUNDLE_WITHOUT` is particularly useful for Rails applications, where there are typically “assets” and “development” gem groups containing gems that are not needed when the app runs in production

For information about using this variable, see http://blog.cloudfoundry.com/2012/10/02/polishing-cloud-foundrys-ruby-gem-support.

`BUNDLE_WITHOUT=assets`

### DATABASE_URL

The Ruby buildpack looks at the database\_uri for bound services to see if they match known database types. If there are known relational database services bound to the application, the buildpack sets up the DATABASE_URL environment variable with the first one in the list.

If your application depends on DATABASE\_URL being set to the connection string for your service, and Trucker does not set it, you can set this variable manually.

`$ truck set-env myapp DATABASE_URL mysql://b5d435f40dd2b2:ebfc00ac@us-cdbr-east-03.cleardb.com:3306/ad_c6f4446532610ab`

### GEM_HOME

Location where gems are installed.

`GEM_HOME:/home/vcap/app/vendor/bundle/ruby/1.9.1`

### GEM_PATH

Location where gems can be found.

`GEM_PATH=/home/vcap/app/vendor/bundle/ruby/1.9.1:`

### RACK_ENV
This variable specifies the Rack deployment environment --- development, deployment, or none. This governs what middleware is loaded to run the application.
 
`RACK_ENV=production`

### RAILS_ENV
This variable specifies the Rails deployment environment ---  development, test, or production.  This controls which of the environment-specific configuration files will govern how the application will be executed.  

`RAILS_ENV=production`

### RUBYOPT
This Ruby environment variable defines command-line options passed to Ruby interpreter.

`RUBYOPT: -I/home/vcap/app/vendor/bundle/ruby/1.9.1/gems/bundler-1.3.2/lib -rbundler/setup`

## Variables Defined by Node Buildpack

### BUILD_DIR
Directory into which Node.js is copied each time a Node.js application is run. 

### CACHE_DIR

Directory that Node.js uses for caching.

### PATH

The system path used by Node.js.

`PATH=/home/vcap/app/bin:/home/vcap/app/node_modules/.bin:/bin:/usr/bin`