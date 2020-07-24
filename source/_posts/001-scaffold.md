---
title: Scaffolding a Quarkus Application
date: 2020-07-23 21:44:11
tags: quarkus, maven, gradle
---
Quarkus offers different ways to scaffold a new application:

- A Quarkus application using Maven as build tool
- A Quarkus application using gradle as build tool
- A Quarkus application using its website

## A Quarkus application using Maven as build tool
Assuming you have installed Maven on your machine, you just need to use the quarkus-maven-plugin as follows:

```sh
mvn io.quarkus:quarkus-maven-plugin:1.6.1.Final:create \
      -DprojectGroupId=org.acme \
      -DprojectArtifactId=mvn-quarkus-app \
      -DclassName="org.acme.quickstart.HelloWorldResource" \
      -Dpath="/hello"
```

## A Quarkus application using Gradle as build tool
In this case you also need to have installed Maven on your machine. Then, you just need to use the same quarkus-maven-plugin with an additional parameter as follows:

```sh
mvn io.quarkus:quarkus-maven-plugin:1.6.1.Final:create \
      -DprojectGroupId=org.acme \
      -DprojectArtifactId=gradle-quarkus-app \
      -DclassName="org.acme.quickstart.HelloWorldResource" \
      -Dpath="/hello" \
      -DbuildTool=gradle
```

## A Quarkus application using its website
Quarkus also offers the ability to scaffold an application using the Quarkus website. This is the most simple way to do it, you only to visit the following link https://code.quarkus.io/ and click on the `Generate your application button`

![https://code.quarkus.io/](/quarkus/images/001-scaffold-images/scaffold-quarkus-website.png  "https://code.quarkus.io/")

Now, you're ready to have fun. If you decided to use maven as the build tool for your project, you need to use the following maven goal to deploy the app:

```sh
mvn compile quarkus:dev
```

If you decided to go for gradle, then you need to use the following gradle task:

```
./gradlew quarkusDev
```

Now, feel free to use any rest client to hit the default endpoint created. If you like curl, use the following curl command to test the app and you will get a similar result as follows:

```sh
$ curl http://localhost:8080/hello
hello
status=200  size=5 time=0.026 content-type="text/plain;charset=UTF-8"
```

So far so good, your first Quarkus app is up and running. That's all folks!