---
layout: post
title: "Getting started with Go language"
published: true
categories:
    - technical
tags:
    - go
    - programming
---

# Development environment for [Go](https://golang.org/) language

Just then I spent about 15 minutes setting up my Mac as a development machine for _go language_.  This is a concise list of steps to follow to setup a similar environment.

## Steps to perform:
* Download the latest Go distribution from [Go language website](https://storage.googleapis.com/golang/go1.6.2.darwin-amd64.pkg)
* Open the downloaded package and follow the instructions
* The go runtime is installed at `/usr/local/go/bin`
* Edit `~/.bash_profile` and make following changes

```bash
# Configuration for GO language
export GOPATH=~/Projects/go-workspace   # this will be the parent directory of all your GO projects
# Ensure go binaries are in the PATH
export PATH=$PATH:/usr/local/go/bin:$GOPATH/bin
```

* Done! You should be good to Go...
