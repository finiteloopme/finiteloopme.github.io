---
layout: post
title: Integration with JBoss Fuse -- Overview
published: false
categories:
    - technical
tags:
    - Integration
    - jboss-fuse
    - how-to
---

# Overview of JBoss Fuse

[JBoss Fuse][471ee006] is a lightweight _enterprise service bus (ESB)_ with an elastic footprint that supports integration in the cloud.
JBoss Fuse allows deployment in several different configurations facilitating intelligent integration - on premise or in the cloud.

## Components of JBoss Fuse
1. Apache Camel - compose your integration applications using standard _Enterprise Integration Patterns (EIPs)_
2. Apache CXF - allows web service (SOAP and REST) integration
3. Apache ActiveMQ - provides core messaging with ESB and integration with external applications
4. Apache Karaf - offers a lightweight [OSGi](http://www.osgi.org/Main/HomePage) based container
5. Fabric8 - simplifies management of distributed JBoss Fuse deployments from a central location

## Getting started
We will be using [JBoss Development Studio (JBDS)][ec7a0d7a] as the development IDE.

1. Create a simple [maven][faab988c] project with packaging type as _jar_
2. Ensure following JBoss Fuse specific repositories are added to the project

    ``` xml
    <repositories>
        <repository>
            <id>release.fusesource.org</id>
            <name>FuseSource Release Repository</name>
            <url>http://repo.fusesource.com/nexus/content/repositories/releases</url>
            <snapshots>
                <enabled>false</enabled>
            </snapshots>
            <releases>
                <enabled>true</enabled>
            </releases>
        </repository>
        <repository>
            <id>ea.fusesource.org</id>
            <name>FuseSource Community Early Access Release Repository</name>
            <url>http://repo.fusesource.com/nexus/content/groups/ea</url>
            <snapshots>
                <enabled>false</enabled>
            </snapshots>
            <releases>
                <enabled>true</enabled>
            </releases>
        </repository>
    </repositories>

    <pluginRepositories>
        <pluginRepository>
            <id>release.fusesource.org</id>
            <name>FuseSource Release Repository</name>
            <url>http://repo.fusesource.com/nexus/content/repositories/releases</url>
            <snapshots>
                <enabled>false</enabled>
            </snapshots>
            <releases>
                <enabled>true</enabled>
            </releases>
        </pluginRepository>
        <pluginRepository>
            <id>ea.fusesource.org</id>
            <name>FuseSource Community Early Access Release Repository</name>
            <url>http://repo.fusesource.com/nexus/content/groups/ea</url>
            <snapshots>
                <enabled>false</enabled>
            </snapshots>
            <releases>
                <enabled>true</enabled>
            </releases>
        </pluginRepository>
    </pluginRepositories>
    ```
3. Add appropriate JBoss Fuse version as a property to the pom

    ``` xml
    <jboss.fuse.version>6.2.1.redhat-069</jboss.fuse.version>
    ```

4. Include the JBoss Fuse BOM so the dependencies are managed automatically

    ``` xml
    <dependencyManagement>
		<dependencies>
			<dependency>
				<groupId>org.jboss.fuse.bom</groupId>
				<artifactId>jboss-fuse-parent</artifactId>
				<type>pom</type>
				<scope>import</scope>
				<version>${jboss.fuse.version}</version>
			</dependency>
		</dependencies>
	</dependencyManagement>
    ```

  [471ee006]: http://www.redhat.com/en/technologies/jboss-middleware/fuse "Red Hat JBoss Fuse"
  [ec7a0d7a]: http://www.jboss.org/products/devstudio/overview/ "JBoss Development Studio"
  [faab988c]: https://maven.apache.org/ "Apache Maven"
