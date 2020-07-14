---
layout: post
title:  Securing EC2 Instance
description: In this post i'll show you how to secure your EC2 instance using ssh and IAM roles and policies
date:   2020-07-14
---
This post is based on my learnings from [FooBar's video](https://www.youtube.com/watch?v=9dpPCJxtHe0). If you prefer to see the video instead, please click [here](https://www.youtube.com/watch?v=9dpPCJxtHe0)

When you are trying to access any AWS service from your EC2 instance (virtual server), giving proper permission and securing your EC2 is essential. You should always follow the best practice known as "least privilege" principle for permissions which means that only give your EC2 least amount of permissions that it needs to get the job done.

Along with the permissions you should also make sure that your EC2 instance is secured as you don't want anyone hacking into your server and steal all the important files you have. Securing an EC2 instance can be done via Secure Shell (SSH) and Security Groups which are nothing more than virtual firewalls.

## Prerequisites:
*   AWS Account
*   Familiarity with Cloud9
*   AWS CLI (verify `aws --version`)
*   `pip3` from Python3 (verify pip3 --version))

If you don't have pip3 (Python3) or CLI installed, run the below commands

pip3 from Python3 - `sudo apt-get install python3-pip3`

AWS CLI - `sudo pip3 install awscli`

## Assumptions:
This blog assumes you know how to:
*   Spin up and EC2 instance
*   Create an S3 bucket and upload a file into it

## What's Covered
*   SSH into EC2
*   IAM Policies and Roles

### SSH into EC2 
SSH is an application that enables you to access another Linux machine from a remote connection
*   Run your instance in Cloud9 / Terminal
*   Run the command to ssh in your instance
```
ssh -i <path-to-private-key> ec2-user@<server's-IP-Address>
```

where:

`-i` - means to "include"

`<path-to-private-key>` - whereever you stored your private key. Private keys come in different formats such as `.pem`(default format), which is obtained from AWS console and Putty Private Key (`.ppk`), for using in Putty for windows machines only.

`ec2-user` - what's the user you want to connect to on the other machine

`<server's-IP-Address>` - this would be an actual IP adress such as 11.2.445.55

**Bonus Tip:**
If you want to only create a key pair manually,then you can do the following steps:
*   Go to EC2 Dashboard
*   On the left menu scroll down to **Network and Security**
*   Click on **Key Pairs**
![](/assets/images/2020-07-14/key-pair.png)

Always use keys and for extra security, put a passphrase (like a password) on the key too.

*   Now, got to **AWS console**, 
*   Create an S3 bucket 
*   Upload a file into it. 
*   Then copy the bucket name
*   Now run the below command
```
aws --region <region-of-the-bucket> s3 ls s3://<name-of-the-bucket>
```

You will get and error saying:

> Unable to locate credentials. You can configure credentials bu running "aws configure"

This is becuase IAM Roles or profile are not yet configured for the EC2 instance. Now we need to create and attach a role to the EC2 instance.

### IAM Roles and Policies
Essentially, what an IAM role is the ability to give a non-human set of permission to do a specific task. It's can be attached to an EC2 instance, another service or another AWS Account, Web identity or SAML Federation. We will be giving permission to EC2 instance (in our case) to read the files in S3 bucket.
*   Go to **AWS Console**
*   Click on **Services**
*   Under **Security, Identity, & Compliance**, click **IAM**
*   On the left scrollable menu, click on **Roles**
*   Click **Create role** button
*   Under **Choose a use case**, choose **EC2**
*   Click **Next: Permission** in the bottom right
*   We won't attach any policies(permissions) yet as there is an interesting aspect with EC2 that I found from the [video]() at 13:40 which is a service in a background that provides API credentials to that instance and rotates every 15 minutes for us. So, will see how we go from no credentials to having credentials without policies to prove that we have the credentials in place.
*   No tags needed
*   Give the role a name
*   Click **Create role**

Now that we have the role, we need to attached it to EC2 instance to be able to use it.

*   Go EC2 Dashboard
*   Right click on your EC2 instance
*   Choose **Instance Settings**
*   Click **Attach/Replace IAM Role**
*   On the next page, click on the dropdown box for **IAM role** and select the role you just created.
*   Click **Apply**

Now run the below command again:
```
aws --region <region-of-the-bucket> s3 ls s3://<name-of-the-bucket>
```
You should see the below error:
>   An error occured (Access Denied) when calling the ListObjectV2 operation: Access Denied
which shows we can access the bucket with our credential however to list the files in the bucket, we still need to set policy(permission) for it and then attach the policy to our role.

*   Go to **AWS Console**
*   Click on **Services**
*   Under **Security, Identity, & Compliance**, click **IAM**
*   On the left scrollable menu, click on **Policies**

We have three types of policies, Customer Managed (custom), AWS managed(AWS built in) and AWS managed - job function
![](/assets/images/2020-07-14/policy-types.png)
*   Select the **Visual Editor** tab
*   For **Service**, click **Choose a Service**
*   In search bar, type **S3**
*   Click **S3**
*   Under **Access Level**, tick **List** and **Read** 
*   For **Resources** choose **Specific**
*   For **bucket**, click **Add ARN**
*   Enter the bucket name in the text box
*   Click **Add**
*   For **object**, click **Add ARN**
*   Type the bucket name in **Bucket name** text box for bucket level access to list all the file in the bucket
*   Type `*` (wildcard) for **object** as we need to get object (files) level access in the bucket.
*   Click **Review Policy**
*   Give your policy a name
*   Give a description
*   Click **Create Policy**
*   Now attach the policy to your previously created role.

Now run the below command again:
```
aws --region <region-of-the-bucket> s3 ls s3://<name-of-the-bucket>
```
and you should see all the files in your bucket.

If it didn't work for you, feel free to get in touch with me. I'd be happy to help
