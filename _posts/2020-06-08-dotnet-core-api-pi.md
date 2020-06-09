---
layout: post
title:  "Basic ASP.NET Core Web API Connected To PostgreSQL Deployed with CI/CD pipeline to Raspbian"
description: This guide shows how to deploy an ASP.NET Core Web API connected to PostgreSQL database to Raspberry Pi using CI/CD Pipeline
date:   2020-06-08
---
The whole idea behind the migration was to eliminate AWS costs as it was hosted in AWS at first.

## Architecture
At the end, our architecture will look like:

<p align="center">
  <img src="/assets/images/2020-06-08/architecture.png">
</p>

If you don't have an ASP.NET Core Web API already, follow the instructions [here](https://blogs.roarcoder.dev/posts/dotnet-core-api/index.html) to create one and skip to **Create a New Project section and onwards**

## Requirements
1.  Raspberry Pi
2.  PostgreSQL
3.  .NET Core 3.1 [SDK](https://dotnet.microsoft.com/download/dotnet-core/thank-you/sdk-3.1.300-linux-arm32-binaries) and [Runtime](https://dotnet.microsoft.com/download/dotnet-core/thank-you/runtime-aspnetcore-3.1.4-linux-arm32-binaries)
4.  AWS Account
5.  Nginx

## Setting up .NET Core 3.1 in Raspberry Pi

SDK and Runtime had to be installed as the "already made" API was targeting framework 3.1.

1.  Download `3.1.300` SDK binary from [here](https://dotnet.microsoft.com/download/dotnet-core/thank-you/sdk-3.1.300-linux-arm32-binaries) in your downloads folder.

2.  Download `3.1.4` runtime binary from [here](https://dotnet.microsoft.com/download/dotnet-core/thank-you/runtime-aspnetcore-3.1.4-linux-arm32-binaries) in your downloads folder.

3. To install the downloaded binaries, make a folder called `dotnet` inside home directory
```
mkdir -p $HOME/dotnet
```
4.  Extract the downloaded SDK folder into `dotnet` folder created previously:
```
tar zxf ~/Downloads/[name-of-the-folder] -C $HOME/dotnet
```
This will take a few moments

5.  Extract the downloaded runtime folder in step 2 into `dotnet` folder created previously:
```
tar zxf ~/Downloads/[name-of-the-folder] -C $HOME/dotnet
```

6.  At the moment the `dotnet` commands can only be run from the home folder. However, if you want to run it from anywhere, you need to export them into environmental variables like below:
```
export DOTNET_ROOT=$HOME/dotnet
export PATH=$PATH:$HOME/dotnet
```
To permanently add the commands, you will have to modify the bash profile with a text editor. So, open `.profile` which should in your home directory
```
sudo vim .profile
```
Add the two exports statements in the file at the end. Save and Exit by pressing `ESC` then `:wq`

7. To verify the installation, run:
```
dotnet --info
```
You should see something similar to this:

<p align="center">
  <img src="/assets/images/2020-06-08/dotnet-info.png">
</p>

If it doesn't work, you might have to reboot Raspbian, `sudo reboot`

Now that we have .NET Core installed, let's move to the next phase

## Set up Postgres server

You can follow the the instructions to install postgres from [here](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-postgresql-on-ubuntu-18-04) as I followed this too.

## Connect To Database

Following the instructions [here](https://blogs.roarcoder.dev/posts/dotnet-core-api/index.html) HOWEVER skip to the section **_Connecting to Database with PostgreSQL DB Management Tool_**


## Build and Run the application

Now, test the application by navigating to it's folder and run `dotnet build`. If all goes well it will build successfully. Then run `dotnet run` to run the application. 

If both commands ran succesfully, you will be shown a URL `http://localhost:5000`. Open that in the browser and go to your api by suffixing [/api/[your-controllers-name`] to the URL like 
```
`http://localhost:5000/api/[your-controllers-name]
```
It should show the data from your Postgres database (if there is any data in the database table of course)

If all the above goes well, run 
```
dotnet build --configuration Release
```
**Code Explanation**

The above command will build the project with Release configuration. 

Copy all the files from `/[your-project-name]/bin/Release/.netcoreapp3.1/` folder to `/var/www/[your-sites-name-folder]/` by running:
```
cp -r ~/workspace/v2/bin/Release/.netcoreapp3.1/* /var/www/[your-sites-folder]/
```
**Code Explantion**

The above ommand will copy all the DLLs and associated files and folders to run the application in your websites folder.

Now create a new file on the root of your project and name it `buildspec.yml`. This will be used by AWS Codebuild to build the project in the AWS Pipeline and add the below code:
```
version: 0.1

env:
  parameter-store:
    DOT_NET_CORE_API: /codebuild/dotNetCoreApi

phases:
  build:
    commands:
      - echo "******** Building ASP.NET API ********"
      - dotnet build --configuration Release
  post_build:
    commands:
      - echo "******** Get ssh key from S3 ********"
      - aws s3 cp s3://ssh-keys-zain/encrypted_id_rsa $HOME/encrypted_id_rsa
      - aws kms decrypt --ciphertext-blob fileb://$HOME/encrypted_id_rsa --output text --query Plaintext | base64 --decode > $HOME/decrypted_id_rsa
      - chmod 400 $HOME/decrypted_id_rsa
      - echo "******** Upload to Raspberry Pi ********"
      - scp -r -oStrictHostKeyChecking=no -i $HOME/decrypted_id_rsa ./bin/Release/netcoreapp3.1/* pi@$DOT_NET_CORE_API:/var/www/dotnet.roarcoder.dev
      - sudo systemctl restart [your-file-name].service
    finally:
      - rm $HOME/decrypted_id_rsa
      - rm $HOME/encrypted_id_rsa
```
**Code Explantion:**

`DOT_NET_CORE_API: /codebuild/dotNetCoreApi` - Domain/IP of your Pi stored in SSM

`dotnet build --configuration Release` - Build the project with Release configuration

`aws s3 cp s3://ssh-keys-zain/encrypted_id_rsa $HOME/encrypted_id_rsa` - Downloads ssh key from S3 to the home directory

`aws kms decrypt --ciphertext-blob fileb://$HOME/encrypted_id_rsa --output text --query Plaintext | base64 --decode > $HOME/decrypted_id_rsa` - Decypts the key downloaded

`chmod 400 $HOME/decrypted_id_rsa` - Give only read permissions to read the key

`scp -r -oStrictHostKeyChecking=no -i $HOME/decrypted_id_rsa ./bin/Release/netcoreapp3.1/* pi@$DOT_NET_CORE_API:/var/www/dotnet.roarcoder.dev` - Transfer files from netcoreapp3.1 to website folder

- `sudo systemctl restart [your-file-name].service` restart the service


## Run API project as a service using DLLs file

Run the project as a service because you want the API to be running always.

1. Create a file `[your-file-name]` with an extension `service` in `/etc/systemd/system/` folder:
```
cd /etc/systemd/system/
touch dotnet-core-service.service
```

2. Paste the below code into it and replace the paths with your paths
```
[Unit]
Description=ASP .NET Web Application
[Service]
WorkingDirectory=[/path-to-your-dotnet-app-dll-files]
ExecStart=[path-to-your-dotnet-folder] [/path-to-your-dotnet-app-dll-files/your-app-name.dll]
Restart=always
RestartSec=10
SyslogIdentifier=dotnet-web-api
User=[your-user]
Environment=ASPNETCORE_ENVIRONMENT=Production
[Install]
WantedBy=multi-user.target
```

3. Now enable the file by running:
```
sudo systemctl enable [your-file-name].service
```

4.  Start the service
```
sudo systemctl start [your-file-name].service
```
5. Verify that it's running
```
sudo systemctl status `[your-file-name].service`
```
<p align="center">
  <img src="/assets/images/2020-06-08/service-file.png">
</p>

## Upload Project to Github

Now create a new github repo by going to their website. Uploaded your local project to the remote repository (assuming you already how to).

## Create a Pipeline and Deploy to Pi

Follow the instructions on my other [blog](https://blogs.roarcoder.dev/posts/hosting-at-home/index.html) - skip to the section **Uploading to Pi via CI/CD pipeline**

**_IMPORTANT_**
* Choose the Github repository you made
* Choose the image `aws/codebuild/standard:4.0` for step 6.4 of the above mentioned blog

If everything goes well, your pipeline will be created and as soon as you commit to Github, the pipeline will be triggered.

If the build failed due to S3 bucket has denied access, make sure the CodeBuild service role is added in the KMS key policy explained here.