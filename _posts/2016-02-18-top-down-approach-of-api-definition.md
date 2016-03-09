---
layout: post
title: "Top down approach of API definition"
published: false
categories:
    - technical
tags:
    - api
    - api-management
    - api-documentation
    - swagger
    - apiman
    - wadl
---

# What is top-down API definition?
In a previous article, we discussed the need for describing APIs.  
One of the most common approaches to designing APIs is creating API definition in a top-down fashion.  This would include following steps:

1. Create an API definition document using a simple text editor or a special editor like [Swagger Editor][5e4b1e18].
2. Using the API definition created in step 1, generate server-side code.

## Creating API definition
Let us use the following sample API definition for this exercise.
``` json
swagger: "2.0"
info:
  version: "1.0"
  title: "Hello from FiniteLoop"
paths:
  /hello/{user}:
    get:
      description: Returns a greeting to the user!
      parameters:
        - name: user
          in: path
          type: string
          required: true
          description: The name of the user to greet.
      responses:
        200:
          description: Returns the greeting.
          schema:
            type: string
        400:
          description: Invalid characters in "user" were provided.
```

  [5e4b1e18]: http://editor.swagger.io/ "Swagger Editor"
