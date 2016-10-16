---
layout: post
title: "Running reports on activities out of Trello"
published: false
categories:
    - technical
tags:
    - api
    - nodejs
---

# Reporting on activities in Trello

```java
/* Get members list from AppDev dashboard */
// Get all of the information about the boards you have access to
var success = function(successMsg) {
  asyncOutput(successMsg);
};

var error = function(errorMsg) {
  asyncOutput(errorMsg);
};

Trello.get('/boards/561466285457d13b4cbe942a/members', success, error);
```
