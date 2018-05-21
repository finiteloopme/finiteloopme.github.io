---
layout: post
title: "Synchronising writes in Microservices"
published: false
categories:
    - technical
tags:
    - kubernetes
    - openshift
    - programming
    - persistent-storage
---

# Synchronising writes in Microservices

## Overview

Adoption of _microservices_ leads to a collection of services, each delivering a well defined feature or capability.  An advantage of this pattern is ability to _horizontally scale_ individual services to service the demand.

As an example, consider a simple _point of sale_ application, which is made up of three _microservices_:
1. ValidateBOM - to validate the _bill of material_
2. Payment - to process the payment
3. LoyaltyCard - to assign loyalty points to the customer

Imagine it is 
[1]: 

---

[^1]: 
