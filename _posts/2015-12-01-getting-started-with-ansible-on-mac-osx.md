---
layout: post
title: "Getting started with Ansible on Mac OS X"
published: false
categories:
    - technical
tags:
    - conf-management
    - how-to
    - mac-os-x
---
# Getting started with Ansible on MAC OS X

[Ansible][2a8cc890] is a simple, agentless, and powerful application deployment, configuration management, and orchestration system.

### Control Machine
Control machine is a central point (can even be a laptop) which can manage an entire fleet of remote machines (also known as *managed nodes*).

This article outlines a step-by-step guide to get started with configuration management using Mac OS X as a *controller machine*.

### Managed Nodes
Ansible by default manages machines over SSH channel.  By default this uses sftp.  User can easily switch to <kbd>scp</kbd> via configuration in <kbd>ansible.cfg</kbd>.

## Installing the control machine on Mac OS X
Preferred way to install ansible on a Mac is using <kbd>pip</kbd>.

    ```bash
    # Install the Python package manager
    $ sudo wasy-install pip
    # install ansible
    $ sudo pip install ansible
    
    ```
  [2a8cc890]: http://www.ansible.com/ "What is Ansible"
