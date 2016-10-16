---
layout: post
title: Building a microservices based application
published: true
categories:
    - technical
tags:
    - microservices
    - wildfly-swarm
    - programming
    - how-to
---

# An application based on principles of microservices
Recently, there has been a lot of discussion and focus on building applications based on the design principles of _microservices_.  Microservices as an architectural principle is a fairly abstract construct.  Definition or template of a _microservices based application (micro-app)_ is going to be subjective, atleast in the foreseeable future.

It is important that an organisation, a group, or a team agree on their own definition of _micro-app_.

For the purpose of this discussion, _micro-app_ is defined as below:

1. Must be a self contained unit.  All the dependencies of the functional component should be packaged into a single deployable unit.
2. A simple command to start/stop the application.
3. Executing the same command _n-times_ will the start _n_ instances of the _micro-app_.

## A sample application
We will build a simple *Echo Service* in Java using REST on HTTP.  The service will provide a well-defined endpoint.  When a request hits this endpoint, the service will respond with a simple *echo* message.

In a traditional model, a (Java) REST service would be deployed in a container like a Tomcat or JBoss WildFly.  These containers inherently have multiple *active* (lazy loading or at startup time) services.

But in our simple example, we do not require these extra services like transactions, messaging, etc.  We need the ability to identify and package only the services that our application requires.

[WildFly Swarm][0c0b573e] offers an innovative approach to packaging and executing Java EE applications by allowing the developer to explicitly specify the required platform features.  In our case we require JaxRS + CDI and the required dependency is specified in pom.xml as follows.

```xml
<dependency>
    <groupId>org.wildfly.swarm</groupId>
    <artifactId>wildfly-swarm-jaxrs-weld</artifactId>
    <version>${version.wildfly.swarm}</version>
</dependency>
```

Now we will configure pom.xml to build us a *fat jar* so that our application can be launched using simple <kbd>java</kbd> command.

```xml
<plugin>
    <groupId>org.wildfly.swarm</groupId>
    <artifactId>wildfly-swarm-plugin</artifactId>
    <version>${version.wildfly.swarm}</version>
    <executions>
        <execution>
            <goals>
                <goal>package</goal>
            </goals>
        </execution>
    </executions>
</plugin>
```

<kbd>mvn clean install</kbd> will build the application for us.  The built binaries will be available in first-wildfly-app/target folder.

1. first-wildfly-app-0.0.1-SNAPSHOT.war - is the binary that can be deployed into a traditional WildFly container.
2. first-wildfly-app-0.0.1-SNAPSHOT-**swarm**.jar - is our _micro-app_ which is the *uber jar* that contains all the required dependencies and just enough of the required JavaEE platform features.

Interacting with the _micro-app_:

```bash
# launch the application
java -jar first-wildfly-app-0.0.1-SNAPSHOT-swarm.jar
# Echo service should be available at localhost:8080/echo
curl http://localhost:8080/echo
```

> TODO: Executing the command multiple times starts the wildfly-swarm server multiple times, which causes startup failure as the ports are already being used.

## Source code
Full source code for this sample application is available as a [github project](https://github.com/finiteloopme/first-wildfly-app).

  [0c0b573e]: wildfly-swarm.io "WildFly Swarm"
