---
layout: post
title:  "S3 Lifecycle Policies and Storage Tiers & Glacier"
description: Brief explanation on S3 Lifecycle Policies, Storage Tiers and Glacier
date:   2020-07-31
---
In this post I will be explaning about S3 Lifecycles policies, storage tiers for S3 and Glacier which I learnt from [here](https://www.youtube.com/watch?v=QMe5SdnI9OE).

## What is S3
Abbreviation of Simple Storage Service which is a storage service from AWS.

## S3 Storage Tiers

When you upload a file to S3, you need to choose a storage class which are also called storage tiers as below:

1. **Standard**
    *   Selected by default.
    *   For files that a frequently accessed
    *   Stores 2 copies in each of the 3 different availability zones (1 or more datacentres in a region)
    *   Costs US$0.023 per GB/Month (most expensive)
2. **Intelligent-Tiering**
    *   Keep tracks of how frequently files are accessed using Machine Learning (ML). So, if you don't use it often, it will move the files to Standard-IA and if you start using it frequently again, it will move it back to the Standard automatically
    *   Stores files for at least 30 days
    *   Useful when you are not sure how often you will accessing the files
3. **Standard-IA**
    *   IA stands for **I**nfrequent **A**ccess
    *   Keeps files for at least 30 days
    *   For files you don't access frequently, for example once a month / once every 2 months e.t.c
4. **One Zone-IA**
    *   For files that are accessed only when something goes wrong eng backup file. 
    *   Kept in only 1 zone
5. **Glacier**
    *   To backup files
    *   For files accessed after a long term. For example once a year e.t.c
    *   Takes minutes to hours to retrieve the file 
    *   Stores file for at least 90 days
    *   Pricing differs per region
6. **Glacier Deep Archive**
    *   Take more than 12 hours to retrieve the file
    *   Stores files for at least 6 months
    *   Costs extremely low

For more information about the above, please click [here](https://aws.amazon.com/s3/storage-classes/)


## S3 Lifecycle Rule
These are the rules set on the S3 object (files) that you want S3 to take action during the duration of the file is kept in S3. For example, you create a rule to transfer a file from Standard to Standard-IA if it's not accessed within a week.

You can also set expiry (deletion) for those object too by specifying number of days which will automatically delete the file after set amount of days have passed.

Hope this post was useful.