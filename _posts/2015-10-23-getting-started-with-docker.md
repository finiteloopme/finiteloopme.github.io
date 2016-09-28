---
layout: post
title: Getting started with docker
published: true
categories:
    - technical
tags:
    - docker
    - how-to
---

# Getting started with Docker
This technical writeup should help the reader get started on working with docker.

## Basic setup
We will be using Mac OSX as the host operating system for this exercise.

1. [Install](https://docs.docker.com/installation/mac/) docker toolbox. We are using version 1.8.3
2. Launch a docker quickstart terminal - *(docker-terminal)*

## Basic commands
1. List all the current *running* docker containers  

    ```bash
    docker ps
    ```

2. List all the docker images

    ```bash
    docker images
    ```

3. Remove (delete) a specific docker image

    ```bash
    # Delete a docker image  
    docker rmi DOCKER_IMAGE_ID
    # A command to force delete all the images
    docker images | awk '{print $3}'| xargs docker rmi --force=true
    # A command to force delete all the previously executed containers
    docker ps -a | awk '{print $1}' | xargs docker rm --force=true
    ```

4. Download a specific docker container

    ```bash
    # Download the latest version of 'centos' docker image from the docker registry
    docker pull centos:latest
    ```

## Docker "Hello World"
1. Launch a docker-terminal
2. Use _centos_ docker image to <kbd>echo</kbd> a message

    ```bash
    # Execute a docker image named 'centos' and execute the 'echo' command
    docker run centos echo 'Hello World!'
    ```

3. Run a docker container in background

    ```bash
    # Execute a docker image named 'centos'
    # Options:
    # --name name to identify the running container
    # -i make the container interactive
    # - P publishes all the ports of the container
    # -d run the container in the background
    docker run --name=demo-container -i -P -d centos
    ```

4. Attach to a running container

    ```bash
    docker attach [conatiner-name]
    ```

5. Attach to a running container in an interactive mode via a shell

    ```bash
    docker exec -i -t [container-name] bash
    ```

## Docker File
A [_dockerfile_](https://docs.docker.com/reference/builder/) is a plain text document that contains all the commands a user would issue to build an image.  Using <kbd>docker build</kbd> user can create an automated build that executes several command line instructions in succession.

```dockerfile
# Dockerfile to build a docker image with FFmpeg framework
# ---------------------------------------------------------------------------
# Build command
# docker build -t ffmpegt --file=[dockerfile-name] .
# Run command
# docker run -it -d --name ffmpeg -P ffmpegt
# ---------------------------------------------------------------------------
FROM centos:latest
MAINTAINER kunal@finiteloop.me
ENV dlpackage http://ffmpeg.org/releases/ffmpeg-2.8.1.tar.bz2
ENV ffmpeg ffmpeg-2.8.1
ENV softwaredir /var/ffmpeg

# Download the required utilities
RUN yum install -y curl tar bzip2
# Create a directory for the software package
RUN mkdir -p ${softwaredir}
# Download the software package
RUN curl ${dlpackage} -o ${softwaredir}/${ffmpeg}.tar.bz2
RUN bunzip2 ${softwaredir}/${ffmpeg}.tar.bz2 && \
tar -xvf ${softwaredir}/${ffmpeg}.tar -C ${softwaredir}
```
