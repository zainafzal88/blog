---
layout: post
title:  Serverless with Terraform
description: In this post I will share my learning about Terraform from HashiCorp
date:   2020-07-18
---
I will share my learnings about Terraform from [this](https://www.youtube.com/watch?v=NsMZ8QedDh8) video. If you are more of a reader than a watcher, please continue reading, Alternatively, watch the [video](https://www.youtube.com/watch?v=NsMZ8QedDh8).

***This post is not about advertising Terraform over Cloud Formation, instead my new learnings about Terraform from the above linked video.***

## What's Covered
*   What Is Terraform?
*   Creation Trigger
*   Why Use It?
*   Serverless.tf
*   Serverless.tf's major features
*   Serverless.tf's Future

## What is Terraform
A software tool that creates and manages infrastructure as code. It's created by HashiCorp.

## Idea Origin
The idea came from Cloud Formation (CF). It is the advanced version(i'd say) of CF which means that Terraform is an extension of CF. If you can't do something with CF, Terraform allows you to do that "something". 

## Why Use It
Terraform can be used by multiple cloud providers such as Google Cloud Provider (GCP), Azure and AWS unlike CF which is AWS specific. Another amazing feature is that we can use same syntax to create resources in multiple cloud providers however, different code (obviously). You might think why use Terraform if I am using AWS only. Well, you'd want to spend as less time as possible in learning and more time implementing. Terraform allows your to get exisitng code and reuse / extend it as it comprises of modules and reuseable components. There are "already made" modules developed by the creators of Terraform that are there to be reused such as VPC, Security Groups etc. CF doesn't allow that.

## Serverless with Terraform
As serverless (concept) comes with tooling, infrastructure and application deployment, and all these are connected to each other in order to work. That is somewhat a complicated structure as there is always something like "BUT in CF, this needs to be done first".

Terraform solves this problem as it handle dependencies better than anything else. What do I mean by that? It handles the manipulation of the resources in your IaC automatically preserving the order This is done by a framework, **Serverless.tf**.

## Serverless.tf
Opinionated framework for fully managing and securing serverless applications and infrastructure on AWS with the help of Terraform. It simplifies serverless for all

## Serverless.tf Major Features
1. Seperate step for each software development lifecycle phase (build package and deploy)
2. Use Terraform AWS Lambda for Lambda tasks
3. Write code and use Terraform instead of CF
4. IaC is the best way to go
5. Gets and install dependances itself using AWS modules such as:
    * Source Paths
    * Patterns to include / exclude
    * Commands to run
    * Docker dependencies used inside on Lambda
6. Supports deployment and increase the application version itself too
7. Any Terraform modules can be used
8. No need for additional plugins
9. CI/CD compatibility

## Serverless.tf's Future
As it's mainly used by HashiCorp internally to work with the technology they work in within the company, more features will be implemented as the need arises within their orgnaisation. However, as it's an open source framework, developer community are encourage to can make modules / contributions.

Please go to [Serverless.tf](https://serverless.tf/) as the awesome creators need your feeback on the framework.