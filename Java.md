## Example: Magnolia

Setting up an 'empheral' deployment to play with Magnolia is very easy:

  1. Download the [Magnolia Tomcat Bundle](http://sourceforge.net/projects/magnolia/files/magnolia/Magnolia%20CE%205.1.1/magnolia-tomcat-bundle-5.1.1-tomcat-bundle.zip/download).
  2. Extract the downloaded archive and `cd` into the`magnolia-5.1.1/apache-tomcat-7.0.40/webapps/magnoliaAuthor` folder.
  3. Push it using the [Java buildpack](https://github.com/cloudfoundry/java-buildpack)!
     ```bash
     cf push --buildpack https://github.com/cloudfoundry/java-buildpack
     Name> magnolia
     
     Instances> 1
     
     1: 128M
     2: 256M
     3: 512M
     4: 1G
     Memory Limit> 4
     
     Creating magnolia... OK
     
     1: magnolia
     2: none
     Subdomain> magnolia
     
     1: cfapps.io
     2: none
     Domain> cfapps.io
     
     Binding magnolia.cfapps.io to magnolia... OK
     
     Create services for application?> n
     
     Save configuration?> y
     
     Saving to manifest.yml... OK
     Uploading magnolia... OK
     Preparing to start magnolia... OK
     -----> Downloaded app package (107M)
     Initialized empty Git repository in /tmp/buildpacks/java-buildpack/.git/
     -----> Downloading OpenJDK 1.7.0_45 from http://download.pivotal.io.s3.amazonaws.com/openjdk/lucid/x86_64/openjdk-1.7.0_45.tar.gz (7.0s)
            Expanding JRE to .java (1.0s)
     -----> Downloading Tomcat 7.0.47 from http://download.pivotal.io.s3.amazonaws.com/tomcat/tomcat-7.0.47.tar.gz (3.5s)
            Expanding Tomcat to .tomcat (0.1s)
     -----> Downloading Buildpack Tomcat Support 1.1.1 from http://download.pivotal.io.s3.amazonaws.com/tomcat-buildpack-support/tomcat-buildpack-support-1.1.1.jar (0.0s)
     -----> Uploading droplet (144M)
     Checking status of app 'magnolia'...
       0 of 1 instances running (1 down)
       ...
       0 of 1 instances running (1 down)
    Push unsuccessful.
    TIP: The system will continue to attempt restarting all requested app instances that have crashed. Try 'cf app' to monitor app status. To troubleshoot crashes, try 'cf events' and 'cf crashlogs'.
     ```

## Additional Information

  * http://blog.cloudfoundry.com/2013/09/06/introducing-the-cloud-foundry-java-buildpack/