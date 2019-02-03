+++
date = "2019-02-03T12:00:00"
draft = "false"
title = "Creating Scala docker images with Google Jib and Maven"
slug = "creating-scala-docker-images-with-google-jib-and-maven"
+++

## tl;dr:

Project template to dockerize a Scala application using [Jib](https://github.com/GoogleContainerTools/jib).

[**Source on github**](https://github.com/olsio/scala-101/tree/master/scala-docker)

## What's Jib

[Jib](https://github.com/GoogleContainerTools/jib) is a Google project to easily create optimized [Docker](https://www.docker.com/) images.

Main project goals:

- **Fast** - Deploy your changes fast. Jib separates your application into multiple layers, splitting dependencies from classes. Now you don’t have to wait for Docker to rebuild your entire Java application - just deploy the layers that changed.

- **Reproducible** - Rebuilding your container image with the same contents always generates the same image. Never trigger an unnecessary update again.

- **Daemonless** - Reduce your CLI dependencies. Build your Docker image from within Maven or Gradle and push to any registry of your choice. No more writing Dockerfiles and calling docker build/push.

Jib consists of a [Maven](https://github.com/GoogleContainerTools/jib/tree/master/jib-maven-plugin) and [Gradle](https://github.com/GoogleContainerTools/jib/tree/master/jib-gradle-plugin) but unfortunatly no [SBT](https://www.scala-sbt.org/) version.

## Making it work with Scala

As SBT is not an option right now the integration has to happen either through [Maven](https://maven.apache.org/) or [Gradle](https://gradle.org/). In this example we are using Maven.

If you want to continue using SBT you could build your artifact using SBT and then just have it as a dependency in a Maven/Docken project to build the final image.

This example will not be using SBT but Maven to build the Scala project and docker image creation.

Enabling the Jib plugin is straight forward. Simply add the jib plugin artifact in the `build:plugin` section of the `pom.xml`. To succesfully build an image the plugin needs some help with finding the main class of the Scala project. This can simply be fixed by adding `mainClass` entry in the `configuration` section of the plugin.

Final [pom.xml](https://github.com/olsio/scala-101/blob/master/scala-docker/pom.xml)

```xml
<plugin>
    <groupId>com.google.cloud.tools</groupId>
    <artifactId>jib-maven-plugin</artifactId>
    <version>0.9.0</version>
    <configuration>
        <mainClass>com.example.QuickstartServer</mainClass>
    </configuration>
</plugin>
```

## Building a local docker image

```
mvn clean compile jib:dockerBuild
```

## Running Application in Docker

```
docker run -p 8080:8080 scala-maven:1.0.0-SNAPSHOT
```

Output:

```
Server online at http://0.0.0.0:8080/
```

## Further reading

Check out the [Jib Maven plugin](https://github.com/GoogleContainerTools/jib/tree/master/jib-maven-plugin) page to see all configuration parameters.

- [Introducing Jib — build Java Docker images better](https://cloudplatform.googleblog.com/2018/07/introducing-jib-build-java-docker-images-better.html)

- [📺 Build containers faster with Jib, a Google image build tool for Java applications](https://www.youtube.com/watch?v=H6gR_Cv4yWI)
