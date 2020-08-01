---
layout: post
title:  "Microsoft Azure Fundamentals - Part 1"
description: Study Guide for AZ-900 - Azure Fundamentals Certification
date:   2020-08-01
---
As I prepare for Azure Certification from [this](https://www.udemy.com/course/azure-fundamentals-az-900-certification/) course, I'll be sharing my learnings about Azure in these series of posts. 

You can get a overview of what skills will be measured in the exam from [here](https://docs.microsoft.com/en-us/learn/certifications/azure-fundamentals#:~:text=Azure%20Fundamentals%20certification%20is%20an,cloud%2Dbased%20solutions%20and%20services.) and click the link shown below to download the document.

  <p align="center">
    <img src="/assets/images/2020-08-01/download-link.png">
  </p>

## Cloud Computing
It's when you have all your computer processes and everything needed to run your application running on third party's(organisation like Microsoft) infrastructure. You "rent" the equipment and pay them for what you use only.

## Benefits of the Cloud Service

The below are the benefits of the cloud services:

* **High Availability (HA)**

  The ability to keep the services up and running with little to no downtime. 

* **Scalability**

  A way to add or remove resources **manually** based on the demand

* **Elasticity**

  A way to add or remove resources **automatically** based on the demand

* **Agility**

  When you want things to happen quickly on the go. 

* **Fault Tolerance**

  This is when application or the services fails to operate but as there is always a backup, there will always be running from the backup copy even if the master copy become faulty.

* **Disaster Recovery (DR)**

  This is a measurement of how quickly or fast a system can recover from failure.

## Economies of Scale
This is the ability do things in an extremely cost effective manner however, more efficiently. This is partciularly useful in the large business as compared to a smaller one. Why? Because you can buy what equipment you need to use in bulk. More you buy, the better price you get.

## Capital Expenditure (CapEx)
WHen you spend on hardware expenses such as physical infrastructure. It's an upfront cost but as the time passes, it only reduces. For instance, servers etc. However, it is deductible from tax beecuase it's a business expense.

## Operation Expenditure (OpEx)
When you spend money running the product or services that need running on day to day bases. This has no upfront cost and it's based on consumption based model (discussed below).

## Consumption Based Model
This model strictly abides by the principle, "Pay for what you use only" as the name "consumption based" says.

## Cloud Types
### Public
* Most common type
* Also know as the Hosting Provider
* Provides service to individual and mulitple companies over the public internet however, through a secure connection
* Cheapest option

## Private
* A company is given a dedicated part of the cloud in which they use the resource provided by the cloud provider also, which they own and operation and can be only accessed from the users of that company only. 
* The organisation / compnay remains fully responsible for their part of the cloud.
* Cheaper option

## Hybrid
Combination of Public and Private clouds that allows you to run application in the most suitable place you think you can run them on spreading on-perm and public and private clouds

## Infrastrcutre-as-aSerivece (IaaS)
* This is the basic category of cloud computing services.
* Rent server, VMs, networks, OS and storage, space in the datacenter from the cloud providers on pay as you go basis
* Instant computing infrastructure created, initiated and handled over the internet
* No CapEx involved

## Platform-as-a-Service (PaaS)
* Allows to focus majorly on application
* Everything in IaaS comme with it
* Helps to create application faster without having to focus on the management of the infrastructure

## Software-as-a-Service (SaaS)
* Get a software itself
* Allows to connect to the software and use cloud based apps
* Everything in IaaS and Paas comes with it
* All you need to do is configure and run it
* Example can be Microsoft Office 365, email and calendar
* No update of application required

## Regions
* Geographical area which has multiple datacenter (discussed below) that are very close to each other with a low-latency (time it takes for the data to travel from point A to point B) network
* There are 54 regions across the world at present in 140 countries

### Special Aure Regions
These are the kind of regions for legal and compliance purposes which means that the data in these regions cannot go out from them and are as follows:
* Azure Government (for US only)
* Azure Germany
* Azure China 21Vianet

### Region Pairs
Each region is paired with another region in the same geography. For example, US East, US West. This assists in data replication. If services from one region fail due to DR such as flood, power outage etc, other region's services kick in as a backup.

## Datacenters
Physical building located in each regions.

## Geographies
* Contain 2+ regions
* Preserves data in the region
* Broken into the following regions:
  * Americas
  * Europe
  * Asia Pacific
  * Middle East
  * Africa 
* Allows you to keep your data and application close to your own region if your need is based on specific data-residency and compliance
* Not all services are available in all regions

## Availability Zones (AZ)
* Phyiscally seperate location in each region
* Each AZ has 1+ datacenters
* Minimum of 3 AZs in a region
* Located in an isolation boundary
* If one dies or breaks down, the other continues to work

## Availability Set
Approach to ensure that your application remain online all the time when the high-impact maintenance event or hardware failures occur. They are made up of:
* **Update Domains (UD)**
  * For Software failure 
  * See the below image to understand this example - When your application is running on multiple machines on UD0 and UD1 (for example). If an update is applied on the application running in UD0 and it fails for any reason, the operation will stop there and only continue to update application running on UD1 when UD0 is fixed and properly functioning
* **Fault Domains (FD)**
  * For hardware failure
  * See the below image to understand this example - When your application is running on multiple machines on FD0 and FD1 (for example). If an update is applied on the application running in FD0 and it fails for any reason, the operation will stop there and only continue to update application running on FD1 when FD0 is fixed and properly functioning

  <p align="center">
    <img src="/assets/images/2020-08-01/azure.png">
  </p>
  
##  Resource Groups
* Every sevice in Azure reside in a Resource Group.
* Can be called as a container for Azure services
* Groups services together in a group



