---
layout: post
title: "API Management in Retail vertical"
published: true
categories:
    - technical
tags:
    - architecture
    - api-mannagement
    - apis
    - design-pattern
---
# Designing API management platform for a large retailer
An API *-Application Programming Interface-* driven economy forms the backbone of the ongoing digital revolution.  In simple terms, API allows a business to offer consistent, secure, and reusable business functions to the market.  This approach is critical to enable a consistent customer experience when engaging with the business, irrespective of the channel used.

Ability to offer the same business service on a different plan is the added benefit of API driven economy.  E.g. being able to offer the same service as part of Gold, Silver, or Bronze plan.

A very large retailer in Australia, with approximately 1000 supermarkets, 1000 mini-stores, and 1000 specialist stores like liquor, etc is in the midst of establishing an API strategy.  This article will refer to the retail as *RA*.  

## Special case of retail business
Recently, I had the opportunity to discuss with *RA* their API strategy.  It would be fair to say that security was the number 1, 2, and 3 cross-cutting concern for RA.  It is easy to understand why with the customer information available in various loyalty programs/systems.  The recent publicity around overseas retailers' customer databases being compromised has placed extra focus on securing services at each layer.

> Just because Service A can access Service B doesn't mean Service A should be allowed to Service B.  Service A should be explicitly granted permission to access Service B.

Secondly, the nature of brick and mortar retail business is such that IT support systems within individual stores must be operational irrespective of the central IT systems being online or offline.  According to the technical team at *RA*, redundancy is built into every layer of the IT system at each store.  For e.g. a dual redundant internet link to the central IT office, data syncing / offline capability within handheld devices used in stores, etc.

Third point of distinction is that local stores maintain *replica* of a few of the services provided by central IT systems.  Functions like authentication, pricing policy, etc need to be controlled by individual stores.

In addition, the IT systems at each store are very tightly controlled with a very rigid deployment schedules.  Deploying *extra* hardware/infrastructure at these stores is extremely cost prohibitive.  As such a minimal footprint is the ideal profile for services running in local stores.

## Parallels with the logistics industry
Logistics industry shares the similar IT concerns with the retail industry.

### 1. Securing devices/services at the edge
With number of devices and services being deployed in the field (trucks in case of logistics; and stores for retailers)
### 2. Loosely coupled with central IT system
Ability to remain operational, irrespective of the central systems being online or offline
### 3. Operating local systems/services
### 4. Small footprint of the services
### 5.


# TODO
- [ ] Comparing multiple devices in the trucks and number of stores leads it to IoT style of problem area.
- [ ] Section on how IoT is closely related to Logistics and Retail
- [ ] Section on Design pattern/architecture used by Logistics industry to address above concerns
- [ ] Overview diagram of the *ideal* architecture
- [ ] Do we really a section of RA's preferred architecture? Initial thought is to not criticise their architecture but simply offer an alternative.
