---
layout: post
title:  "Microsoft Azure Fundamentals - Part 2"
description: Study Guide for AZ-900 - Azure Fundamentals Certification
date:   2020-08-04
---
This is a continuation of [Part 1](https://blogs.roarcoder.dev/posts/azure-learnings/index.html). If haven't already read that, you should go though that before continuing further.

## Resource Group (continued...)
Picking up where I left in [Part 1](https://blogs.roarcoder.dev/posts/azure-learnings/index.html).
* Used for monitoring meter anad builing at Resource Group level
* Also set up policies
* Set up alerts (if a certain service goes above the certain threshold, it will alert you)
* Set up Quotas (for example: set up to use only certain amount for storage.)
* Controll access level (who can control what to a what level)
* Delete Resource Group = Delete all service in the group
* Each resource can only exist in one Group
* Can be spreak across regions
* Add / remove resources from resource group at any time
* Delete resource from one group and add to another
* Application can your resource from different resources groups

## Azure Resource Manager
It's a tool to manage your resource groups

## Compute
* Service that's used to run your cloud based applications on.
* Provides resources such as virtual disks, machines, processors, memory, networking and OSs
* Available whenever you need it and can be ran in seconds to minutes

### Virtual Machines (VM)
* Literally computers but you access them remotely
* Emulators of physically computers

#### VM Scale sets
* Group of indentically configured VMs

### App service
* Platform as a service
* Helps doing everything related to apps (build, deploy and manage)

### Functions
* Create resources based event in codes

## Containers
* Virtual environments
* OS less
* Lightweight
* Created, scaled out and can be stopped dynamically

### Container Services
#### Azure Container Instances
* Platform-as-a-Service
* Allows to upload your container and it runs it.

#### Kubernetes Service
* Automatically manages large number of your containers