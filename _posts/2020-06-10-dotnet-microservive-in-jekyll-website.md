---
layout: post
title:  Personal Website Building Journey and Next steps
description: This shows you what steps I took to build my personal website with minimal costs and what I plan to do next.
date:   2020-06-12
---
I wanted to build my own website to create my personal branding. I saw my friend's CV in Jekyll, so I decided to make my website in it too. There are many other static site generators also, if you don't want to use Jekyll.

At first, I hosted the website in AWS using S3, Cloudfront and Route53. This gave me a lot of hands on experience in AWS as I was preparing for my exam, Solution Architect simultaneously. I got to know ins and outs of the above mentioned AWS services. Then, I wanted to go a step further. Hence, I converted the monolithic structure of my website into microservices, Frontend and backend using AWS SAM (below section) and deployment using CI/CD pipeline as suggested by Paul. Having done this, after few weeks, I realised it was costing me a bit. Then I decided to host the personal website entirely in Raspberry Pi (see below). 

The overall structure looks like this:

<p align="center">
  <img src="/assets/images/2020-06-12/personal-website.png">
</p>

The above architecture covers how my website looked when it was hosted in AWS, where the word **OR** is and then my setup on Raspberry Pi.

This post contains:

1.  The summary of how to setup webserver on Pi. For detailed steps, see [how to setup Pi as a webserver](https://blogs.roarcoder.dev/posts/hosting-at-home/index.html) and to upload already made Jekyll site to Pi

2.  Setting up API to be consumed by Jekyll frontend using AWS SAM (API Gateway + Lambda + DynamoDB)

3.  How I converted microservice in AWS SAM written in Node to .NET Core 3.1

## Setup Pi as a Webserver & Upload Website
In order to set up Pi as webserver, you need to:

1.  Download, burn, and install Raspberry Pi OS on SDCard
2.  Connect Pi to the internet
3.  Enable SSH.
4.  SSH into Pi
5.  Install Nginx
6.  Configure Port Forwarding to access the web server on public internet

To see detailed steps, click [here](https://blogs.roarcoder.dev/posts/hosting-at-home/index.html).

After all the above is done, `scp` your static website from your client(in my case Mac) to the server in `/var/www/html`.
Now, you should beable to see your site when you access your Pi's IP

## API on AWS using AWS SAM

My website was a monolithic structure, however best practice in the modern web development is to use microservice structure. So, my friend, Paul, suggested me to convert it into microservice and load the data dynamically.

1.  FrontEnd Microservice
2.  Backend Microservice

I will only discuss backend microservice that contains user information. 

Create an API using SAM so that my personal information is fetched from this API and displayed in the frontend. I will use the below Cloud Formation template.
```
AWSTemplateFormatVersion: '2010-09-09'
Transform: AWS::Serverless-2016-10-31
Description: >
  SAM Template for user layer backend microservice
  

Globals:
  Function:
    Timeout: 3

Resources:
  GetUserFunction:
    Type: AWS::Serverless::Function 
    Properties:
      Handler: getuser.lambdaHandler
      Runtime: nodejs10.x
      Policies:
        - AmazonDynamoDBReadOnlyAccess
      Environment:
        Variables:
          TABLE: !Ref UsersTable
      Events:
        GetUserApi:
          Type: Api 
          Properties:
            Path: /getuser/{proxy+}
            Method: get
  UsersTable: # dynamo db table
    Type: AWS::Serverless::SimpleTable
    TableName: users
    Properties:
      PrimaryKey:
          Name: UUID
          Type: String
    ProvisionedThroughput:
        ReadCapacityUnit: 5
        WriteCapacityUnits: 5
    Tags:
        AppType: Serverless

Outputs:
  GetUserApi:
    Description: "API Gateway endpoint URL for Prod stage for Hello World function"
    Value: !Sub "https://${ServerlessRestApi}.execute-api.${AWS::Region}.amazonaws.com/Prod/getuser/"
  GetUserFunction:
    Description: "Get User Lambda Function ARN"
    Value: !GetAtt GetUserFunction.Arn
  GetUserFunctionIamRole:
    Description: "Implicit IAM Role created for Get User function"
    Value: !GetAtt GetUserFunctionRole.Arn
```
 The above template creates
 
 1. Lambda function
 2. API Gateway
 3. AWS DynamoDB

 API Gateway triggers Lambda function, which in turn, gets the specific record from DynamoDB. Below is the code for lambda

 ```
const AWS = require('aws-sdk')
const dynamodb = new AWS.DynamoDB.DocumentClient({region: 'ap-southeast-2'})

  exports.lambdaHandler = async (event) => {

    let uuid = event['pathParameters']['proxy']
    const params = {
        TableName:process.env.TABLE,
        Key: {
            UUID: uuid
        },
        ProjectionExpression:"firstName, lastName, title, email, url_LinkedIn, url_Github, occupation, url_Blogs"
    }

    const getUserInfo = await dynamodb.get(params).promise()
    let response;
        
    try {
        response = {
            statusCode: 200,

            // Enabled Cors
            headers: {
                "Access-Control-Allow-Origin": "*"
            },
            body: JSON.stringify(getUserInfo.Item, null, 3)
        };
    }
    catch (err) {
        response = {
            statusCode: 200,
            body: "An error occured: " + err
        };
    }

    return response
};
 ```
The above lambda function reads the unique identifier `uuid` from the url parameter and gets that record from DynamoDB (you should insert the records in database table manually) database and enables Cross Origin Resource Sharing (CORS).

In my frontend, I load the data using `AJAX` which calls my API and extracts all the needed information to be displayed
 ```
 $(function () {

    $.ajax({
        type: 'GET',
        url: '[api-url]',
        success: function(user){
            $('#fullName').text(user.firstName + " " + user.lastName)
            $('#occupation').text(user.occupation)
            $('#email').attr('href', 'mailto:' + user.email)
            $('#linkedIn').attr('href', user.url_LinkedIn)
            $('#github').attr('href', user.url_Github)
            $('#blogs').attr('href', user.url_Blogs)
        }
    })
})
 ```

## API in .NET Core 3.1
If you don't prefer Node, I have written the same API in using C# [here](https://blogs.roarcoder.dev/posts/dotnet-core-api/index.html) - skip to **Create a New Project** section


## Next Steps
I will be updating the content and design of my website whenever any change happened in my career.