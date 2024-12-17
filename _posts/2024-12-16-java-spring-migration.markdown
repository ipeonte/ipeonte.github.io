---
layout: post
title:  "Java 8 + SpringBoot migration and problems with externalizing configuration."
date:   2024-12-16 20:11:17 -0800
categories: jekyll update
---
Disclaimer  
Article below related to Java + SpringBoot ecosystem.

# Intro
Since Java 9 it switched to [Project Jigsaw](https://openjdk.org/projects/jigsaw/)  and modular platform system it also changed the principle of loading shared libraries that worked for years in previous versions of java.

# Side effect of Java 9 Platform Module System (JPMS)
As it indicated [here](https://docs.oracle.com/javase/10/migrate/toc.htm#JSMIG-GUID-2C896CA8-927C-4381-A737-B1D81D964B7B) since Java 9 the extension mechanism was removed, it means no more lib/ext folder where usually all shared jar’s were stored. It’s makes complete sense for new modular java application however it raises questions how to handle situations when some dependencies needs to be externalized.
    
    > no more lib/ext folder

# Problem with SpringBoot extrnalization
Spring Boot externalization gets affected by this change, for example externalizing database configuration in Spring Boot JPA. Core service might have platform independent logic based on the JPA abstraction layer and actual driver and configuration can be stored outside of executable JAR. 

# The Solution

Step #1:
Add <configuration><layout>ZIP</layout></configuration> into spring-boot-maven-plugin configuration.
Explanation: “layout” parameter defines the package type, for ex. war, jar etc. Layout “ZIP” is the same as jar but it using PropertiesLauncher.

![Figure 1: Simplified Microservice Architecture](/assets/images/SpringBootMavenConfig.png)  
Figure 1: spring-boot-maven-plugin configuration


The [PropertiesLauncher](https://docs.spring.io/spring-boot/specification/executable-jar/property-launcher.html) is a special SpringBoot class loader that can load additional classes before the lib folder from the executable jar.

One of the parameters for PropertiesLauncher is “loader.path” that can have absolute or relative path but the problem here is that it’s required “file:” prefix.  Unfortunately it’s not clearly explained in documentation.

Finally the solution to externalize database configuration will look as next

```shell
java  -Dloader.path=file:/usr/share/java -jar  service.jar --spring.config.location=mysql.properties 
```
In Debian the /usr/share/java folder is where all shared java jar files are installed by default.
