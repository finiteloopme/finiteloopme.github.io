---
layout: post
title: Developing Mobile App using Red Hat Mobile Application Platform
published: false
categories:
    - technical
tags:
    - mobile
    - development
    - rh-map
---

# Developing mobile app using Red Hat MAP

## Background
Recently I worked with an Australian University on an integration initiative to standardise the way people and businesses *get in touch* with the University.  This involved building a single facade or an entry point deployed on an ESB.  With a plan that all the channels would be redirected to communicate with the University via this facade.

Well governed external facing APIs offer obvious benefits at the backend of a simplified and consistent way of handling incoming service requests.  The added benefit is that new channels can be added to *consume* these services without having a cascading impact on downstream services.

## The Mobile Application
With a nicely constructed and governed *Contact Us* service in place, we decided to build a *mobile app* to allow users to contact the University.
