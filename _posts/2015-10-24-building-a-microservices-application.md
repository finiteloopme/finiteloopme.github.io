---
layout: post
title: Building a microservices based application
published: false
categories:
    - technical
tags:
    - microservices
    - wildfly-swarm
    - programming
    - how-to
---

# An application based on principles of microservices
Recently, there has been a lot of discussion and focus on building applications based on the principles of microservices.  Microservices as an architectural principle is a fairly abstract construct.  Definition or template of a _microservices based application (micro-app)_ is going to be subjective, atleast in the foreseeable future.

It is important that an organisation, a group, or a team agree on their own definition of _micro-app_.

For the purpose of this discussion, _micro-app_ is defined as below:
1. _Micro-app_ must be a self contained unit.  All the dependencies of the functional component should be packaged into a single unit.
2. Issue a simple command to start/stop the application.
3. Executing the same command _n-times_ will the launch _n_ instances of the _micro-app_.

## Sample application

## Source code
Full source code for this sample application is available as a [github project](https://github.com/finiteloopme/first-wildfly-app).
