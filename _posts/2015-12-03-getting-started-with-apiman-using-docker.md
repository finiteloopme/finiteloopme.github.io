---
layout: post
title: "Developing APIMan using Docker"
published: true
categories:
    - technical
tags:
    - how-to
    - apiman
    - docker
    - mac-os-x
---
# Developer's guide to start working with APIMan
## What is APIMan?
[APIMan ](http://apiman.io) is an open source project for developing an API management solution.

## About this article
APIMan has a very interesting value proposition.  Developing features, add-ons is a very effective way of getting started with a project.  I couldn't find a *easy* documented way to get started with APIMan - purely *from a development* perspective.  Hopefully this article will help a developer to get started quickly with development on APIMan project.

## Core features of APIMan are:
1. **API governance**: flexible, policy-based runtime governance of the API.  Ability to offer same API through multiple plans, allowing different levels of service to different API consumers.
2. **Rich management layer**: REST API and separate UI to manage/configure not only APIs but also the applications that consume them.
3. **Easily embeddable**: a very small footprint allows to embed API Management Policy Engine in any application.
4. **Fully asynchronous**: the runtime engine's API is fully asynchronous and is designed to run equally well in both a standard Java EE environment and newer async runtimes like [Vert.x](http://vertx.io).

## Running APIMan in a docker container
JBoss community maintains a docker image specifically for APIMan project.  Using this docker image is one of the easiest ways to get started with APIMan.

```bash
# Download the APIMan docker image
$ docker pull jboss/apiman-wildfly
# Run APIMan in a docker container
$ docker run -it -p 8080:8080 -p 8443:8443 jboss/apiman-wildfly
```
> If you are using Mac OS X, make sure that you access the docker container using the IP address of Docker *default* machine, instead of *localhost*

## Developing APIMan using docker

First thing we want to do is build a docker container with *development friendly* APIMan preconfigured.  Then we want to deploy the custom developed artifacts to this container.

1. Fork the [github project](https://github.com/apiman/apiman-wildfly-docker) for APIMan related docker image.
> My fork resides at: https://github.com/finiteloopme/apiman-wildfly-docker

2. Next <kbd>git clone</kbd> our fork so that we can make development specific changes to the APIMan [dockerfile](http://docs.docker.com/engine/reference/builder/).

    ```bash
    $ git clone https://github.com/finiteloopme/apiman-wildfly-docker
    ```

3. Make a copy of *Dockerfile* as *Dockerfile-devel*. Make following changes to the devel dockerfile.

    ```makefile
    # Add an admin user so that we can deploy our code (java artifacts)
    RUN /opt/jboss/wildfly/bin/add-user.sh root Root!23 --silent
    # Start/bind the management service to the local IP
    CMD ["/opt/jboss/wildfly/bin/standalone.sh", "-b", "0.0.0.0", "-bmanagement", "0.0.0.0", "-c", "standalone-apiman.xml"]
    ```
    > At the time of writing this article, the jboss/apiman-wildfly uses Java 7. Where as the latest (master branch) of APIMan requires Java 8. There is an [open issue](https://github.com/jboss-dockerfiles/apiman/issues/19) and a corresponding [pull request](https://github.com/jboss-dockerfiles/apiman/pull/20) on github to upgrade docker image to use Java 8.
    >
    > In the meantime, please use the dockerfiles available at https://github.com/finiteloopme/apiman-wildfly-docker

4. Build the new docker image

    ```bash
    # Change the directory to Dockerfile-devel file location
    $ docker build --file=Dockerfile-devel --tag=jboss/apiman-devel .
    ```

5. Start the docker container

    ```bash
    $ docker run -p 8080:8080 -p 8443:8443 -p 9990:9990 -it jboss/apiman-devel
    ```

6. Add the [wildfly-maven-plugin](https://docs.jboss.org/wildfly/plugins/maven/latest/deploy-mojo.html) configuration to APIMan parent pom file.

    ```xml
    <properties>
        .....
        <!-- Wildfly deploy configuration -->
        <version.wildfly.deploy.plugin>1.1.0.Alpha4</version.wildfly.deploy.plugin>
        <wildfly.deploy.hostname>dockerhost</wildfly.deploy.hostname>
        <wildfly.deploy.mgmt.port>9990</wildfly.deploy.mgmt.port>
        <wildfly.deploy.username>root</wildfly.deploy.username>
        <wildfly.deploy.passwd>Root!23</wildfly.deploy.passwd>
    ....
    <properties>
    <build>
        <pluginManagement>
            <plugins>
                ...
                <!-- Wildfly plugin to deploy artifacts -->
				<plugin>
					<groupId>org.wildfly.plugins</groupId>
					<artifactId>wildfly-maven-plugin</artifactId>
					<version>${version.wildfly.deploy.plugin}</version>
					<configuration>
						<!-- Force the application to be redeployed if it already exists -->
						<force>true</force>
						<filename>${project.build.finalName}.${project.packaging}</filename>
						<hostname>${wildfly.deploy.hostname}</hostname>
						<port>${wildfly.deploy.mgmt.port}</port>
						<username>${wildfly.deploy.username}</username>
						<password>${wildfly.deploy.passwd}</password>
					</configuration>
				</plugin>
                ...
            </plugins>
        </pluginManagement>
    </build>
    ```
    > On Mac OS X, please add a *hostname* for the docker host in ''/etc/hosts' file.  And use this hostname while configuring the wildfly-maven-plugin. Simply using IP address will result in an error.

7. Execute <kbd>mvn wildfly:deploy</kbd> on the project (maven module) and the appropriate artifact will be deployed to the target environment configured in step 6.
