---
layout: post
title: "Designing APIs"
published: true
categories:
    - technical
tags:
    - api
    - api-management
    - api-documentation
    - swagger
    - api-man
    - wadl
---

# Overview of designing APIs?
In an integration scenario, API (_Application Programmable Interface_) is used by a service provider to represent a function that consumers can use.  Consumers would ideally like a stable and simple to consume API.  This requires that the API details are appropriately documented by the API _provider_.  These details should allow users and machines to discover and understand capabilities of the service with _close-to_ no access to source code and implementation details.  

Aim of designing APIs is to address this concern by providing a specification to present APIs in a standard, language agnostic format. There are multiple competing specification/standards working in this space:

1. [Swagger][b78ee9ea]
2. [RAML _RESTful API Modeling Language_][4a89b1e7]
3. [Apiary][66752ae6]
4. [API Blueprint][07f9a72e]

_Swagger_ is getting a fair bit of traction in the market and has  donated their [specification](https://github.com/OAI/OpenAPI-Specification) to [Open API Initiative][0ef3f664].  I will be using Swagger as a reference implementation when discussing API modeling/designing.

> It is worth highlighting that modeling or designing of APIs assists with governance during design (including development) time only.  
Runtime governance is implemented by API Management solutions like [APIMan][955a0479].

## Common patterns with API design
### 1. Top-down approach
In a top-down approach, API designer would use an editor like [Swagger Editor][1400d899] to create an API definition.  Using this definition, a developer can then use [code generation tools][fcacd9c1] to generate a _stub_ for server side implementation.
> API definitions could also be created using a simple text editor.

### 2. Bottom-up approach
In a bottom-up approach, an implementation of API already exists.  A user can then either create the definition manually or in certain cases (if appropriate frameworks like JAX-RS) API definition can be generated automatically.

### 3. Consuming a well designed API
Using an API definition, a consumer can explore the API and generate client library.

## What about WADL?
[WADL][f3f92a32] (_Web Application Description Language_) is a machine readable description of HTTP-based web applications like REST web services.  Writing a WADL manually is an extremely tedious task.

  [b78ee9ea]: http://swagger.io "Swagger"
  [4a89b1e7]: http://raml.org "RESTful API Modeling Language"
  [66752ae6]: https://apiary.io/ "Apiary"
  [07f9a72e]: https://apiblueprint.org/ "API Blueprint"
  [0ef3f664]: https://openapis.org/ "Open API Initiative"
  [955a0479]: http://www.apiman.io/latest/ "API Man"
  [1400d899]: http://editor.swagger.io/ "Swagger Editor"
  [fcacd9c1]: http://swagger.io/getting-started/swagger-codegen "Swagger Code Generation Tools"
  [f3f92a32]: https://en.wikipedia.org/wiki/Web_Application_Description_Language "WADL"
