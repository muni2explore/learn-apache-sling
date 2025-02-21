# DAY 01


1. Start Sling:
```bash

# launcher is a tool that starts up the Sling application
./org.apache.sling.feature.launcher-1.3.2/bin/launcher \
-D org.osgi.framework.bundle.duplicateBSNAllowed=true \
-f org.apache.sling.starter-13-oak_tar_far.far

```

This command is running Apache Sling, which is a web framework that helps developers build web applications. Let me break down what each part of this command means:

1. `./org.apache.sling.feature.launcher-1.3.2/bin/launcher` - This is the path to the launcher program for Apache Sling. The launcher is a tool that starts up the Sling application.

2. `-D org.osgi.framework.bundle.duplicateBSNAllowed=true` - This is setting a system property. In this case, you're telling the OSGi framework (which Sling uses under the hood) to allow duplicate bundle symbolic names. This is addressing the error you were getting earlier about duplicate bundles.

3. `-f org.apache.sling.starter-13-oak_tar_far.far` - This specifies which feature archive file to use. A feature archive (.far file) contains all the components and configurations needed to run a specific version of Sling.

In simple terms, this command is starting up the Apache Sling web framework with a specific configuration, while also applying a fix to allow duplicate bundle names which was causing the errors you saw earlier.

When this command runs successfully, it will start a Sling server on your machine (typically accessible at http://localhost:8080), which you can then use to develop and run web applications.

```bash
mkdir hello-sling
cd hello-sling
```

## Create a minimal `pom.xml` file at the root:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" 
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
    <modelVersion>4.0.0</modelVersion>
    
    <groupId>com.akamai</groupId>
    <artifactId>hello-sling-app</artifactId>
    <packaging>bundle</packaging>
    <version>1.0-SNAPSHOT</version>
    
    <name>Hello Sling App</name>
    
    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
        <maven.compiler.source>17</maven.compiler.source>
        <maven.compiler.target>17</maven.compiler.target>
        <sling.java.version>17</sling.java.version>
    </properties>
    
    <dependencies>
        <!-- Sling API -->
        <dependency>
            <groupId>org.apache.sling</groupId>
            <artifactId>org.apache.sling.api</artifactId>
            <version>2.27.0</version>
            <scope>provided</scope>
        </dependency>
        
        <!-- OSGi annotations -->
        <dependency>
            <groupId>org.osgi</groupId>
            <artifactId>org.osgi.service.component.annotations</artifactId>
            <version>1.5.0</version>
            <scope>provided</scope>
        </dependency>

        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>4.0.1</version>
            <scope>provided</scope>
        </dependency>
    </dependencies>
    
    <build>
        <plugins>
            <!-- Maven Bundle Plugin -->
            <plugin>
                <groupId>org.apache.felix</groupId>
                <artifactId>maven-bundle-plugin</artifactId>
                <version>5.1.8</version>
                <extensions>true</extensions>
                <configuration>
                    <instructions>
                        <Bundle-SymbolicName>${project.artifactId}</Bundle-SymbolicName>
                        <Bundle-Name>${project.name}</Bundle-Name>
                        <Bundle-Version>${project.version}</Bundle-Version>
                        <Export-Package>
                            com.akamai.sling.hello
                        </Export-Package>
                        <Sling-Model-Packages>
                            com.akamai.sling.models
                        </Sling-Model-Packages>
                    </instructions>
                </configuration>
            </plugin>
            <!-- Maven Compiler Plugin -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.11.0</version>
                <configuration>
                    <source>17</source>
                    <target>17</target>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>
```

```bash
mkdir -p src/main/java/com/akamai/sling/hello
```

## Create a simple servlet file at src/main/java/com/akamai/sling/hello/HelloWorldServlet.java

```java
package com.akamai.sling.hello;

import org.apache.sling.api.SlingHttpServletRequest;
import org.apache.sling.api.SlingHttpServletResponse;
import org.apache.sling.api.servlets.SlingSafeMethodsServlet;
import org.osgi.service.component.annotations.Component;

import javax.servlet.Servlet;
import java.io.IOException;

@Component(
    service = Servlet.class,
    property = {
        "sling.servlet.paths=/bin/message",
        "sling.servlet.methods=GET"
    }
)
public class HelloWorldServlet extends SlingSafeMethodsServlet {
    
    private static final long serialVersionUID = 1L;
    
    @Override
    protected void doGet(SlingHttpServletRequest request, SlingHttpServletResponse response) 
            throws IOException {
        response.setContentType("text/html");
        response.getWriter().write("<html><body><h1>Hello, Sling World!</h1></body></html>");
    }
}
```


Now let's understand what each part does:

1. **Project Structure**:
   - `src/main/java`: Your Java source code

2. **POM File**:
   - `packaging: bundle`: Creates an OSGi bundle
   - Dependencies:
     - `org.apache.sling.api`: Core Sling APIs
     - `org.osgi.service.component.annotations`: For component annotations

3. **Servlet**:
   - `@Component`: OSGi component annotation
   - `service = Servlet.class`: Registers as a servlet service
   - `sling.servlet.paths`: URL path to access the servlet
   - `SlingSafeMethodsServlet`: Base class for GET requests

To run:




2. Build and deploy your bundle:
```bash
mvn clean install
# or 
mvn clean package
```

3. Install your bundle through the Felix console:
   - Go to http://localhost:8080/system/console (admin/admin)
   - Click "Bundles" → "Install/Update"
   - Select your bundle from `target/hello-sling-app-1.0-SNAPSHOT.jar`
   - Click "Install or Update"

4. Test your servlet at: http://localhost:8080/bin/message

