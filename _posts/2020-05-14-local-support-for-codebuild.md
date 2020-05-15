---
layout: post
title:  "Setting up CodeBuild locally"
description: Step by step guide to set up and use local support for CodeBuild to build your applications locally
date:   2020-05-14
---
Local support for CodeBuild is extremely useful if you want save money and not use the AWS CodeBuild to build (for testing). Instead, run the builds locally when in development phase and trying to get things working. Once you get it building successfully, use the buildspec.yml in AWS CodeBuild project itself. 

At first, I wasn’t aware of this and I used CodeBuild entirely in learning phase. When I saw my bill, it was shocking. Then my mentor,  Paul Kukiel, recommended me this. It was a life saver.

## Setting up build process

### Step 1

Clone this repository in your workspace

`git clone https://github.com/aws/aws-codebuild-docker-images.git`

![](/assets/images/2020-05-14/step1.png)

### Step 2

Open the repository in your VS Code  and navigate to `/aws-codebuild-docker-images/ubuntu/standard/3.0`

![](/assets/images/2020-05-14/step2.png)

### Step 3

Open Dockerfile present in `/aws-codebuild-docker-images/ubuntu/standard/3.0` to build a local CodeBuild standard 3.0 image

![](/assets/images/2020-05-14/step3.png)

### Step 4

Remove the last line in that file that says `ENTRYPOINT [“dockerd-entrypoint.sh”]`

![](/assets/images/2020-05-14/step4.png)

### Step 5

Run the below commands in /aws-codebuild-docker-images/ubuntu/standard/3.0. This will build the docker image locally. Ensure your Docker is running.

`docker build -t aws/codebuild/standard:3.0 .`

![](/assets/images/2020-05-14/step5.png)

### Step 6

Now we need to setup the local CodeBuild agent

#### Step 6.1

Get the latest by running

`docker pull amazon/aws-codebuild-local:latest --disable-content-trust=false`

![](/assets/images/2020-05-14/step6.1.png)

#### Step 6.2

Run the below commands to download the file codebuild_build.sh that contains all the magic that will be executed to build locally and give it execution permission. 

 If you don’t have “wget” install, run “brew install wget”

`brew install wget` (skip if you already have wget)

`wget https://raw.githubusercontent.com/aws/aws-codebuild-docker-images/master/local_builds/codebuild_build.sh`

`chmod +x codebuild_build.sh`

![](/assets/images/2020-05-14/step6.2.png)

## Buiding A React App Using buildspec.yml and upload to S3

Create React App (CRA) is a tool created by Facebook to create React applications in the single command in terminal. If you don't have an exisiting project, create one with `create-react-app`

`buildspec.yml` file is where all the commands are written to be executed in order to get and build everything the project needs to run successfully

### Step 1:

Navigate to your project's folder and create a file buildspec.yml at the root. The file has to be named exactly that as CodeBuild looks for it to do the build

`cd your-project-folder-name`

`touch buildspec.yml`

### Step 2

Paste the below code in your buildspec.yml file

Add a bucket name in SSM (AWS Systems Manager Parameter Store) as we will fetch the bucket name from there (assuming you know how to)

```
version: 0.2

env:
  parameter-store:
    BUCKET: /codebuild/bucketName

phases:
  install:
    commands:
        - npm install
  build:
    commands:
        - npm run build
        - aws s3 sync build/ s3://$BUCKET
 ```
**Code Explanation**

`version: 0.2` - Represents what version of buildspec is being used. 

`env` -  Represents information for custom environment variables

`parameter-store` -  To retrieve custom environment variable from AWS System Manager Parameter Store (SSM). Store values as key: value pairs

`BUCKET` - Denotes the use of S3 bucket

`/codebuild/bucketName` - Naming convention to name parameters in SSM such as /{serviceName}/{parameterName}

`phases` - CodeBuild has different phases however, we only use install and build. Each of these phases contain their respective commands to run.

`install` - Sections runs commands to install all the necessary tools needed to build installed for the project

`npm install` - Downloads a package an its dependencies

`build` - Section that runs commands required to build the project

`npm run build` -  Outputs the production ready version of the application in a separate folder buildwhich doesn’t contain source code.

`aws s3 sync build/ s3://$BUCKET` - This uploads the contents of build folder to S3. You may have noticed “BUCKET” prefixed with a dollar $ sign. This is necessary for the runtime to know that it’s an environmental variable.

### Step 3

Now, open up terminal and navigate to the clone git repository aws-codebuild-docker-images (see step 1 if unsure) and run below command:

`./codebuild_build.sh -i aws/codebuild/standard:3.0 -a ~/Desktop -s ~/workspace/create-react-app -c ~/.aws`

This is how it’s being used:

`$ codebuild_build.sh [-i image_name] [-a artifact_output_directory] [options]`

`codebuild_build.sh` -  Executes a shell script

`-i` - Specifies the build image (from step 5)

`-a` - Specifies the output directory

`-s` - Specifies directory where buildspec is located

`-c` -  Specifies the folder that contains AWS configurations and credentials (usually in your home directory)

### Step 4

Create S3 bucket and name it as appropriate

### Step 5

If everything goes well, you should see you React application in S3

![](/assets/images/2020-05-14/s3-before-upload.png)

Hope it helped and you’re all up and running. If you faced any problems, let me know in the comments.

![](/assets/images/2020-05-14/s3-after-upload.png)