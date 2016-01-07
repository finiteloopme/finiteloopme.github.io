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
    - cd
---

# Continuous integration and delivery with JBoss Fuse

Aim of this writeup is to assist software engineers (developers and operators) in creating CD (continuous delivery) environments for integration applications developed using JBoss Fuse.

## Benefits of continuous delivery

1. Shorten software lifecycle times
    - Establish concrete pipeline to production
2. Improve feedback loops
    - Does the feature address business concern?
3. Repetition
    - Build, test, and release often.
    - Every build is a *release candidate*
4. Eliminate bottlenecks
    - Automate, automate, and automate again.

## Tools for CD pipeline

1. Docker as a container for JBoss Fuse
2. Git for SCM
3. Gerrit/Gitlab for code review
4. Maven
5. Jenkins + plugins
6. JBoss Fuse for integration (specifically fabric8-maven-plugin)

Source code for this framework is available on [GitHub](https://github.com/finiteloopme/cd-jboss-fuse)

## Fabric8 maven plugin
