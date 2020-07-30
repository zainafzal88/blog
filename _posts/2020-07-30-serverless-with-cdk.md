---
layout: post
title:  "Basic Serverless Application Using AWS CDK"
description: I will take you through building your basic serverless application with AWS CDK
date:   2020-07-30
---
In this post I'll be showing you how to create a very simple serverless application utilizing AWS CDK which I learnt from [here](https://www.youtube.com/watch?v=XVHGq2uJu9s). We will be using API Gateway, Lambda and DynamoDB

##  What is CDK?
It's an acronym for Cloud Development Kit. It's an open source project which allows you to define the cloud services in your preferred programming language. CDK is compatible with JavaScript, TypeScript, Python, Java and C# (my favourite). It's invented by Amazon Web Services which is also known as AWS worldwide.

Now that you know what CDK is, lets build a serverless application using it.

## Pre-requisites
*   AWS Account
*   VS Code or any of you favourite editor

## Install CDK
Run the below command to install CDK if you haven't got it already. Run `cdk --version` to check
```
npm i -g aws-cdk
```

## Create Project
*   Make a directory
```
mkdir hello-cdk
```

*   `cd` into the folder
```
cd hello-cdk
```  

*   Initialize the project by running the below command (it will take a a minute or so)
```
cdk init sample-app --language=typescript
```
where
`sample-app` - is a template
`language` - which ever supported language you want to use. I'll be using Typescript

*   Open the project in VS Code
*   Below is the folder structure
   <p align="center">
    <img src="/assets/images/2020-07-30/folder-structure.png">
   </p>

## Make Changes to the Project

*   `bin` folder
Root of your application. It contains our Cloud Formation stacks. You can add mulitple Cloud Formation stacks as modules here.
    <p align="center">
    <img src="/assets/images/2020-07-30/bin.png">
    </p>
*   `lib` folder
Contains the stack definition. Remove everything, so your `hello-cdk-stack.ts` looks like
    <p align="center">
    <img src="/assets/images/2020-07-30/lib.png">
    </p>
    
We will replace the code we have removed with our infrastructure later
*   `node_modules` folders
Contains node modules

*   `test` folder
Comes out of the box. Remove the tests as they won't work because we have deleted the code above. Now your test file should look like:
    <p align="center">
    <img src="/assets/images/2020-07-30/tests-file.png">
    </p>


## Add AWS Services to the Project
Open `hello-cdk-stack.ts` which is in `lib` folder

As we need three services API Gateway, Lambda and DynamoDB, let's import their libraries and add(install) them in the project from terminal.
<p align="center">
    <img src="/assets/images/2020-07-30/libs-1.png">
    </p>

### Add DynamoDB

```
const table = new dynamodb.Table(this, "Hello", {
    partitionKey: { name: "name", type: dynamodb.AttributeType.STRING },
}); 
```
Above table create a table called `Hello`, assigns it a partition key of `name` of type string

### Add Lambda
```
const dynamoLambda = new lambda.Function(this, "DynamoLambdaHandler", {
    runtime: lambda.Runtime.NODEJS_12_X,
    code: lambda.Code.asset("functions"),
    handler: "function.handler",
    environment: {
    HELLO_TABLE_NAME: table.tableName,
    },
});
```
Above code 
*   Creates a construct using `Function` keyword and we name it `DynamoLambdaHandler`
*   Defines a runtime
*   Tells where the code is (in `function` file which we will be creating soon)
*   Tells where the handler is
*   We pass environmental variable

*   Now create a folder called `functions` in the root
*   Create a file called `function.js` in `functions` folder
*   Copy paste the below code in the `function.js` file

```
exports.handler = async function (event) {
  console.log("request:", JSON.stringify(event));

  // gives response back to upstream caller
  return sendRes(200, "HELLLOOO");
};

const sendRes = (status, body) => {
  var response = {
    statusCode: status,
    headers: {
      "Content-Type": "text/html",
    },
    body: body,
  };
  return response;
};
```

*   Give permissions to lambda to write to our table
```
table.grantReadWriteData(dynamoLambda);
```

### Add API Gateway

*   Ceates an API Gateway called `hello-api` by running:
```
const api = new apigateway.RestApi(this, "hello-api");
```
*   Now we need to add a root and a method to that root
```
 api.root
      .resourceForPath("hello")
      .addMethod("GET", new apigw.LambdaIntegration(dynamoLambda));
```
*   Create outputs so we can see things when they are being deployed and will get the URL for the API Gateway
```
new cdk.CfnOutput(this, "HTTP API URL", {
      value: api.url ?? "Something went wrong with the deployment",
    });
```

## Deploy

Run `cdk deploy` to deploy the stack to Cloud Formation

**Do you wish to deploy these change (y/n)?** Yes

This will take a while. Basically what it does is it runs `synth` and then deploys utilizing Cloud Formation service.

## Verify the Deployment

1.  Go to **AWS Console**
2.  Navigate to **Cloud Formation**
3.  You should see the stack being deployed.

Once it completes, it will give you the API Gateway URL. Copy the URL and test it in Postman and append the url with `hello`. You should see **HELLLOOO** in the response. If yes, Well Done!! You did! If not, leave me a comment below and I'll help you out.