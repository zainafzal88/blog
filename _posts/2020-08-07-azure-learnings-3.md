---
layout: post
title:  "Microsoft Azure Fundamentals - Part 3"
description: Study Guide for AZ-900 - Azure Fundamentals Certification
date:   2020-08-07
---
This is a continuation of [Part 2](https://blogs.roarcoder.dev/posts/azure-learnings/index.html). If haven't already read that, you should go through that before continuing further.

## Azure Network Services
### Azure Virtual Network
*   Insfrastructure as a service to create and utilize VMs in cloud
*   Enable VMs to talk to each other
*   Can also connect between on-prem and Azure
*   Must be within a single region
*   Multiple virtual networks in multiple regions can talk to each other
*   Can't have device in different regions that below to a same vitrual network
*   Restricted to a region

### Azure Load Balancer
*   Balances traffic equal amongst multiple servers

### VPN Gateway
*   Virtual Private Network Gateway
*   Secure connection between on-prem and cloud
*   All traffic will be encrypted

### App Gateway
*   Manages web traffic to application itself
*   Operated on application level

### Content Delivery Network (CDN)
*   Delivers web content to local users
*   Content is hosted in multiple regions
*   User connects to content from a region closest to it

## Azure Storage Services
### Data Categories
Stores any type of data like file, messages, table and virtual machines too. There are different type of categories of storage

**Structured Data**
*   Data for schema (types of object can be created and properties of those objects)
*   Format of databases, example is financial data or Active Directory

**Semi-structured Data**
*   Less orgranized
*   Not stored in a relation format
*   Called non-relational or N0-SQL data

**Unstructured Data**
*   No structure of data what so ever
*   No restriction / limits to what type of data it can store like **blob** can have PDF, JPG, JSON file or video