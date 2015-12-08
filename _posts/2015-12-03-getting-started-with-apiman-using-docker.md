---
layout: post
title: "Getting started with APIMan using Docker"
published: false
categories:
    - technical
tags:
    - how-to
    - apiman
    - docker
    - mac-os-x
---
# Developer's guide to start working with APIMan
[APIMan ](http://apiman.io) is an API management platform provided by Red Hat JBoss open source project.

## Core features of APIMan are:
1. **Govern your APIs**: flexible, policy-based runtime governance of the API.  Ability to offer same API through multiple plans, allowing different levels of service to different API consumers.
2. **Rich management layer**: REST API and separate UI to manage/configure not only APIs but also the applications that consume them.
3. **Easily embeddable**: a very small footprint allows to embed API Management Policy Engine in any application.
4. **Fully asynchronous**: the runtime engine's API is fully asynchronous and is designed to run equally well in both a standard J2EE environment and newer async runtimes like [Vert.x](http://vertx.io).

## Development using docker
### Using the APIMan docker image

    ```bash
    # Download the APIMan docker image
    $ docker pull jboss/apiman-wildfly
    # Run APIMan in a docker container
    $ docker run -it -p 8080:8080 -p 8443:8443 jboss/apiman-wildfly
    ```

> If you are using Mac OS X, make sure that you access the docker container using the IP address of Docker *default* machine, instead of *localhost*

### WebUI endpoints for various components
1. Keycloak
    > http://{hostname}:{port-number}/auth/admin

+ APIMan UI
    > http://{hostname}:{port-number}/apimanui/

+ Existing login
    > admin/admin123!
    >
    > It is strongly advised to change the login details using Keycloak URL

+ Register a service (API)
    > Using [openweathermap.org](http://openweathermap.org) as an example
    > 
    > APPID: 3305a3c7e501ea255d17c0564cf33d9c
