---
layout: post
title:  "Class and script in groovy"
date:   2019-08-10 09:40:49 -0500
categories: DevOps
tags: [DevOps, Jenkins, groovy]
---

## Overview

Pipeline as code which is introduced in the Jenkins 2.0 allows jenkins pipeline to get created as code. Later on, the ability to use groovy functions and classes as the shared libraries allows Jenkins pipeline to be more modular and improved the reusability. However, it does requires some understanding of how class works in groovy scripts

## Class and scripts definition in groovy

Groovy class definition is almost identical to Java Class
    - Class are defined using class keyword
    - it may contain fields, constructors, initializer and methods
    - Methods and constructors may themselves use local variables as part of their variable definition

### Fields and local variables

Same as in Java, Groovy has local variables scoped with the methods and fields which is scope with instance/class (depends on it is static or not)

#### Declaring variable

variables (both local and fields) must be declared before first use.
Scripts allow the use of undeclared variable. These variables are assumed to come form the script's binding and are added to the binding if they are not there.

Groovy uses the same modifiers as Java -- private, protected and public for visibility. Final for not allowing reassignment and static to denote class variables. A non-static fields is also known as an instance variables.

## Reference

<https://proquestcombo.safaribooksonline.com/book/programming/java/9781935182443/part-1dot-the-groovy-language/kindle_split_021_html#X2ludGVybmFsX0h0bWxWaWV3P3htbGlkPTk3ODE5MzUxODI0NDMlMkZraW5kbGVfc3BsaXRfMDE4X2h0bWwmcXVlcnk9>
