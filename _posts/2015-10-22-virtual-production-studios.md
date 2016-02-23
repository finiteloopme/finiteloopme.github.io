---
layout: post
title: Virtual production studios for live events
published: true
categories:
    - business
tags:
    - media
    - PaaS
---

# Virtual production studio

## Why do we need a virtual production studio
**[Outside broadcasting - OB](https://en.wikipedia.org/wiki/Outside_broadcasting)** is a term used for field production done from a location away from a regular *[television studio](https://en.wikipedia.org/wiki/Television_studio)*.  OB is very commonly used during televising of live sports events, music concerts, etc. Raw footage/signal is fed into *[production truck](https://en.wikipedia.org/wiki/Production_truck)* for processing, recording and possibly transmission.

![Media -- Remote Production](/img/2015/10/media-remote-production.png)

These production trucks are really mobile production studios, with all the required applications and equipment required for production and post production of live events. These trucks are a very costly piece of equipment.

The notion of a _virtual production studio (VPS)_ is to virtualise the production and post-production applications and host them in a cloud.  Raw footage/signal would be supplied to the VPS where actual production and post-production of the content would take place.  This will obviously allows the broadcasters the ability to tune their capacity to produce and broadcast live events to meet the actual market demand.  Meaning that the capacity to produce content is not limited by the number of production trucks available.  The added benefit is that costly equipment need not be on the road.
![Virtual production](/img/2015/10/media-virtual-production.png)

## Ideal platform for these applications
So the idea is to move these production applications into a centrally hosted platform.  Important characteristics for the platform would be:

1. Ability to spin up the application platform -VPS- as close to the live event as possible; ideally at the network edge to reduce latency
+ Automation to enable push button launch/deploy of applications.
+ Ability to scale applications (increase or decrease number of instances) to appropriately service number of events being broadcasted.
+ The nature of these applications may also require that the underlying platform be _uncontested_ or must adhere to delivering well defined QoS.

Consider the three (3) basic cloud models for VPS:

1. IaaS: infrastructure as a service
2. PaaS: platform as a service
3. SaaS: software as a service

### IaaS
IaaS enables users to spin up workload from _OS (Operating System)_ level and up.  This obviously gives a fair amount of control to the user regarding how application is packaged.  This requires automation via use of scripting tools. Most commonly used scripting tools are Ansible, Puppet, Chef, etc.  Using these scripts and tools, automation is performed by orchestrating the underlying API provided by the IaaS platform (e.g. AWS, Google Cloud Platform, OpenStack distribution, internal hypervisor based cloud, etc).  

Drawback of this approach is that the automation script ties you into a certain platform.  For e.g. automation scripts developed for AWS would need significant *refactoring* for *Google Cloud Platform*.  And this is a fairly significant disadvantage as VPS needs to be deployed in various locations.  And it won't be valid to make assumptions on availability of specific IaaS platforms.

### SaaS
An application available via SaaS would be an attractive proposition.  Reason being that the SaaS model inherently removes the operational risks.  Though in this scenario given the critical aspect of reducing latency, following three (3) work against SaaS:

1. Shared platform - most of the SaaS applications are multi-tenanted so will be sharing the platform with other tenants/customers.
2. Location - more often than not, SaaS applications are hosted out of a centralised data centre/cloud which could introduce significant network latency.
3. Integration - VPS would require fair bit of integration between encoding, decoding, mixing applications.  Again integrating SaaS based applications involves fair amount of network chatter, potentially introducing latency.

### PaaS
PaaS is deployed on top of IaaS and provides best of both the worlds with enough automation and control from IaaS and the necessary abstraction (hiding of technical details) provided by SaaS.  

Applications developed and deployed on enterprise ready PaaS like [Red Hat OpenShift][4087d7eb] abstracts the applications from the underlying infrastructure.  [Red Hat OpenShift][4087d7eb] can be deployed on almost any infrastructure which can run [RHEL 7][35f68a00].  This allows for a larger install base than developing application deployment scripts for specifc IaaS platform.

Added benefit of this approach is that [Red Hat OpenShift][4087d7eb] facilitates deployment of applications using  micro-services design pattern.  OpenShift is built using widely used functional components like:

1. [Doker][38136ad0] is a very popular implementation of Linux containers. It streamlines packaging of distributed applications.
2. [Kubernetes][4013e689] is orchestration, management, and self-healing engine for Linux container.
3. [Git][75d0b00a] for source code management
4. [Jenkins][9271fe11] for continuous integration framework

This makes PaaS an ideal platform for virtual production studio.
![Virtual production studio on PaaS](/img/2015/10/media-virtual-prod-studio-paas.png)

  [38136ad0]: https://www.docker.com/ "Docker website"
  [4087d7eb]: http://www.openshift.com/ "OpenShift Website"
  [4013e689]: http://kubernetes.io/ "Kubernetes Website"
  [35f68a00]: https://www.redhat.com/en/files/resources/en-rhel-whats-new-in-rhel-712030417.pdf "Data Sheet - Red Hat Enterpise Linux 7"
  [75d0b00a]: https://git-scm.com/ "Open Source version control system"
  [9271fe11]: https://jenkins-ci.org/ "Open Source continuous integration server"
