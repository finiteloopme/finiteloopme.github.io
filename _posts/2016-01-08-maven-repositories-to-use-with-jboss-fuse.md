---
layout: post
title: Maven repositories to use with JBoss Fuse
published: true
categories:
    - technical
tags:
    - development
    - java
    - maven
---

# Maven repos to use for development with JBoss Fuse

For a Java developer, it is important to understand which [Maven](http://maven.apache.org) repositories he or she should be using to resolve all the artifacts and libraries required for the project.

Two important repositories to be aware of when working with JBoss Fuse are listed below.  I haven't been able to locate any official commentary around which repositories would host which releases (versions) of certain artifacts.  I end up including both the repositories in the project's <kbd>pom.xml</kbd> file and it works more often than not.

## 1. JBoss EA repo
This seems to be the *early release* repository for all the JBoss projects; including JBoss Fuse.  Though it is missing some JBoss Fuse releases. E.g. it is missing version **1.2.0.redhat-621084**.

~~~xml
<repository>
    <id>jboss.ea</id>
    <name>JBoss Early Adopter</name>
    <url>http://origin-repository.jboss.org/nexus/content/groups/ea/</url>
</repository>
~~~

## 2. JBoss Fuse Public repo
This seems to be the repository that hosts all the JBoss Fuse artifacts.

~~~xml
<repository>
    <id>jboss.fuse</id>
    <name>JBoss Fuse Public</name>
    <url>https://repo.fusesource.com/nexus/content/groups/public/</url>
</repository>
~~~
