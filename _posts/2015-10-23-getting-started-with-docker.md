---
layout: post
title: Getting started with docker
published: false
categories:
    - technical
tags:
    - docker
    - how-to
---

# Basic setup
We will be using Mac OSX as the host operating system for this exercise.

1. [Install](https://docs.docker.com/installation/mac/) docker toolbox. We are using version 1.8.3
2. Launch a docker quickstart terminal - *(docker-terminal)*

# Basic commands
1. List all the current *running* docker containers

    ``` bash
    docker ps
    ```
2. List all the docker images

    ``` bash
    docker images
    ```
3. Remove (delete) a specific docker image

    ``` bash
    # Delete a docker images
    docker rmi DOCKER_IMAGE_ID
    # A command to force delete all the images
    docker images | awk '{print $3}'| xargs docker rmi --force=true
    # A command to force delete all the previously executed containers
    docker ps -a | awk '{print $1}' | xargs docker rm --force=true
    ```
4. Download a specific docker container

    ``` bash
    # Download the latest version of 'centos' docker image from the docker registry
    docker pull centos:latest
    ```

# Docker "Hello World"
1. Launch a docker-terminal
2. Use _centos_ docker image to <kbd>echo</kbd> a message

    ``` bash
    # Execute a docker image named 'centos' and execute the 'echo' command
    docker run centos echo 'Hello World!'
    ```

3. Run a docker container in background

    ``` bash
    # Execute a docker image named 'centos'
    # Options:
    # --name name to identify the running container
    # -i make the container interactive
    # - P publishes all the ports of the container
    # -d run the container in the background
    docker run --name=demo-container -i -P -d centos
    ```
4. Attach to a running image

    ``` bash
    docker attach [conatiner-name]
    ```
