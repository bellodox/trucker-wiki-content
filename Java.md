Java applications can be deployed to Trucker.io using the [Java buildpack](https://github.com/cloudfoundry/java-buildpack). This buildpack supports:

  * [Tomcat](#tomcat)
  * [Play](#play)
  * [Spring](#spring)
  * [Java Main Class](#main-class)
  * [Groovy](#groovy)
  
If you would like to debug the deployment of an application using the Java buildpack:

```bash
truck set-env <app name> JBP_LOG_LEVEL DEBUG
```

_For each example provided below, please work from within a newly created directory._

## Tomcat

The Java buildpack supports Java Servlets and JavaServer Pages using [Tomcat](http://tomcat.apache.org/). if the `WEB-INF` directory exists in the application root and no main class is detected.

### Example: Magnolia

Setting up an 'empheral' deployment to play with Magnolia is very easy:

  1. Download the [Magnolia Tomcat Bundle](http://sourceforge.net/projects/magnolia/files/magnolia/Magnolia%20CE%205.1.1/magnolia-tomcat-bundle-5.1.1-tomcat-bundle.zip/download).
  2. Extract the downloaded archive and `cd` into the`magnolia-5.1.1/apache-tomcat-7.0.40/webapps/magnoliaAuthor` folder.
  3. Push it using the [Java buildpack](https://github.com/cloudfoundry/java-buildpack)!
     
     ```bash
     truck push --buildpack https://github.com/cloudfoundry/java-buildpack
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
     
     1: ie.trucker.io
     2: none
     Domain> ie.trucker.io
     
     Binding magnolia.ie.trucker.io to magnolia... OK
     
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
       1 of 1 instances running (1 running)
     Push successful! App 'magnolia' available at http://magnolia.ie.trucker.io
     ```

## Play

The Java buildpack supports the [Play Framework](http://www.playframework.com/). In the following subsection is shown how to deploy a Play application.

### Example: Hello World

First [download](http://downloads.typesafe.com/play/2.2.1/play-2.2.1.zip) an [install](http://www.playframework.com/documentation/latest/Installing) the Play Framework.

```bash
play new hello-play
cd hello-play
play dist
truck push --buildpack https://github.com/cloudfoundry/java-buildpack --path target/universal/hello-play-1.0-SNAPSHOT.zip
```

## Spring

[Spring](http://spring.io/)

```bash
./gradlew build
truck push --buildpack https://github.com/cloudfoundry/java-buildpack --path build/libs/yourapp-x.y.z.jar
```

## Main Class

It is possible to run a custom Java application by specifying a class with a `main` function. 

**Server.java**:

```
import java.io.*;
import java.net.*;

public class Server {
  public static final int BACKLOG = 10;

    public static void main(String args[]) throws Throwable {
        String portString = null;
        int port;
        ServerSocket s = null;
        Socket conn = null;
        PrintStream out = null;
        BufferedReader in = null;
        long counter = 0;

        try {
          portString = System.getenv("PORT");
        } catch (Throwable e) {  
        }

        if (portString == null) {
          portString = "8888";
        }

        System.out.println(portString);
        port = Integer.parseInt(portString);
       
        s = new ServerSocket(port, Server.BACKLOG);
          
        while(true) {
          conn = s.accept();
          out = new PrintStream(conn.getOutputStream());
          in = new BufferedReader(
            new InputStreamReader(
              conn.getInputStream()
            )
          );
            
          out.println("HTTP/1.0 200 OK");
          out.println("Content-Type: text/html");
          out.println();
          out.println(
            "<html><head>"
            + "<title>Hello World!</title>"
            + "</head><body>"
            + "<h1>Hello World!</h1>"
            + "<p>This was request: " + (++counter) + "</p>"
            + "</body></html>"
          );
          
          out.flush();
          conn.close();
        }
    }
}
```

Compile and create a jar:

```bash
javac Server.java
jar cfe Server.jar Server Server.class
```

Deploy to Trucker.io:

```bash
truck push --buildpack https://github.com/cloudfoundry/java-buildpack --path Server.jar
```

## Groovy

The Java buildpack supports [Groovy](http://groovy.codehaus.org/) out of the box. Just like above, where it is shown how to run the main method of a Java class, it is possible to run a Groovy application. A simple example is provided below.

**main.groovy**:

```groovy
#!/usr/bin/env groovy
import java.net.*
try {
  port = Integer.parseInt(System.getenv("PORT"))
} catch (Exception e) {
  port = 3000
}

counter = 0

server = new ServerSocket(port)
while(true) {
  server.accept { socket ->
    counter++

    socket.withStreams { input, output ->
      output.withWriter { writer ->
        writer << "HTTP/1.1 200 OK\n"
        writer << "Content-Type: text/html\n"
        writer << "\n"
        writer << "<html><head>"
        writer << "<title>Hello World!</title>"
        writer << "</head><body>"
        writer << "<h1>Hello World!</h1>"
        writer << "<p>This was request: " + counter + "</p>"
        writer << "</body></html>"
      }
    }
  }
}
```

And then just...

```bash
truck push --buildpack https://github.com/cloudfoundry/java-buildpack
```

## Additional Information

  * http://blog.cloudfoundry.com/2013/09/06/introducing-the-cloud-foundry-java-buildpack/
