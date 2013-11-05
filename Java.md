## Example: Magnolia

Setting up an 'empheral' deployment to play with Magnolia is very easy:

  1. Download the [Magnolia Tomcat Bundle](http://sourceforge.net/projects/magnolia/files/magnolia/Magnolia%20CE%205.1.1/magnolia-tomcat-bundle-5.1.1-tomcat-bundle.zip/download).
  2. Extract the downloaded archive and cd into the`magnolia-5.1.1/apache-tomcat-7.0.40/webapps/magnoliaAuthor` folder.
  3. Push it using the [Java buildpack](https://github.com/cloudfoundry/java-buildpack)!
     ```bash
     cf push --buildpack https://github.com/cloudfoundry/java-buildpack
     Name> magnolia
     
     Instances> 1
     
     1: 128M
     2: 256M
     3: 512M
     4: 1G
     Memory Limit> 256M
     
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
     ```

