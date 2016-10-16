---
layout: post
title: Working with projects on Github
categories:
    - technical
tags:
    - github
---

# Collaborating on Github projects

Its safe to say that [github](http://github.com) is one of the most widely used source code management systems for software development.

Every time I start working on a new project on github, I always turn to good old [Google](www.google.com) to *get me started* for the same set of questions.

So here are the steps that I follow to start working on a project on github:

### 1.	[Fork the project code](http://help.github.com/articles/fork-a-repo)

```bash
   # Original project
   https://github.com/apiman/apiman
   # Forked project
   https://github.com/finiteloopme/apiman
```

### 2.	Clone your fork

```bash
   # Clone the project locally
   git clone https://github.com/finiteloopme/apiman
   # This is the master branch of your project
   cd apiman
   # Create a remote repo named *upstream* for the original project
   git remote add upstream https://github.com/apiman/apiman
```

### 3.	Contribute fixes and/or features to the project

```bash
   # Create a branch for a specific issue
   git checkout -b apiman-issue123
   # Switch the branch to *master*
   git checkout master
   # Fetch & merge all the upstream/master changes
   git pull upstream master
   # Switch to the topic on-hand
   git checkout apiman-issue123
   # Reapply changes to master. The latest from master will be the new base of the changes
   git rebase master
   # Push your topic branch into your public fork
   git push origin apiman-issue123
```

### 4.	Generate a [pull-request](http://help.github.com/articles/using-pull-requests) for your changes
