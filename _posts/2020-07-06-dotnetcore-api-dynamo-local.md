---
layout: post
title:  ASP.NET Core API Web API + DynamoDB Locally
description: This is a walkthrough of accessing DynamoDB locally using ASP.NET Core Web API
date:   2020-07-06
---
In this post, I will show you how to connect and read data from DynamoDB locally

## What's Covered
*  Create the ASP.NET Core Web API
*  Integrating with Swagger UI
*  Connecting and reading from DynamoDb locally


## Prerequisites:
* AWS account
* C#
* .NET Core [SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1) and [Runtime](https://dotnet.microsoft.com/download/dotnet-core/3.1) installed (if you haven't got them, install them from the given links)
* Java installed
* .NET CLI

## Create Project

1. Open up terminal
2. Check the dotnet verison installed
```
dotnet --version
```
3. Install Lambda and serverless template
```
dotnet new -i Amazon.Lambda.Templates
```
4. Create a ASP.NET Core Web API using the template installed above:
```
dotnet new serverless.AspNetCoreWebAPI --name CommentsAPI
```
5. cd into the `src` and then project's folder
```
cd src
cd CommentsAPI
```
6. Restore all the dependencies
```
dotnet restore
```
7. Run the project to make sure it's working without errors
```
dotnet run
```
8. Use VS Code or any of your favourite text editor and open the project folder
9. Notice four new files in addition to the normal files new .NET project area created.
<p align="center">
  <img src="/assets/images/2020-07-06/project-folder-content.png">
</p>

  * `aws-lambda-tools-defaults.json` - This file is read by lambda tooling by default. Your function handler is specified in this file. So, if you update the function's name, you need to update it in this file too.
  * `LambdaEntryPoint` - Pretty much self explanatory (file from which lambda executes your application code) when you deploy to AWS
  * `LocalEntryProint`- Local testing entry point when you test the application locally
  * `serverless.template` - SAM template file in JSON which defines insfrastructure as code.

## Integrating Swagger

1. We will be using [Swagger](https://swagger.io/) to document our Web API and test it from the browser. Add NuGet package for Swagger
```
dotnet add package Swashbuckle.AspNetCore
```
2. To use it, you need to register it by adding the below code in the `ConfigureServices` method in `Startup.cs` file
```
services.AddSwaggerGen(c =>{
c.SwaggerDoc("v1", new OpenApiInfo { Title = "Comments API", Version = "v1" });
});
```
3. Add import of `Microsoft.OpenApi.Models` on the top of the page
```
using Microsoft.OpenApi.Models;
```
4. Then, enable it by inserting the below code in the `Configure` method of `Startup.cs` file. Add the below code above the `app.UseHttpsRedirection();` Otherwise, Swagger UI won't load.
```
app.UseSwagger();
app.UseSwaggerUI(c =>
{
    c.SwaggerEndpoint("v1/swagger.json", "Comments");
    c.RoutePrefix = "swagger";
});
```
![](/assets/images/2020-07-06/swagger-config.png)  

5. Run the application and then click the url shown
6. Replace `index.html` in the URL with `swagger` and press Enter
7. If everything went well, you should see Swagger UI now
<p align="center">
  <img src="/assets/images/2020-07-06/swagger-ui.png">
</p>

## Install, Connect and Read from local DynamoDB
To connect and read from DynamoDB, we will need to install it locally first.

### Install DynamoDB locally
1. Follow the steps [here](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/DynamoDBLocal.DownloadingAndRunning.html)

#### Create Table
1. Navigate to the folder where you extracted `DynamoDBLocal.jar`

2. Start DynamoDB locally by running
```
java -Djava.library.path=./DynamoDBLocal_lib -jar DynamoDBLocal.jar -sharedDb
```
   <p align="center">
    <img src="/assets/images/2020-07-06/start-dynamodb-locally.png">
   </p>

3. Open another terminal tab

4. Run the below command to create a table
```
aws dynamodb create-table --table-name commentsTable --attribute-definitions AttributeName=id,AttributeType=S --key-schema AttributeName=id,KeyType=HASH --provisioned-throughput ReadCapacityUnits=5,WriteCapacityUnits=5 --endpoint-url=http://localhost:8000
```
The above command create a table named `commentsTable` in the endpoint `http://localhost:8000`
![](/assets/images/2020-07-06/create-table.png)
5. To check the created table run:
```
aws dynamodb list-tables --endpoint-url http://localhost:8000
```
![](/assets/images/2020-07-06/list-tables.png)

### Connect ASP.NET Core Web API to Local DynamoDB
* Navigate to you project folder. We will add two NuGet packages.These are essential to make a connection to DynamoDB
```
dotnet add package AWSSDK.DynamoDBv2
dotnet add package AWSSDK.Extensions.NETCore.Setup 
```
`AWSSDK.DynamoDBv2` - Add the sufficients support to interact with DynamoDB using AWS .NET SDK
`AWSSDK.Extensions.NETCore.Setup` - has methods for configuration and registrations of AWS Services with dependency injection.

* Now add configuration to **appsettings.json**. This contains configuration for local DynamoDB.
```
"DynamoDb": {
    "LocalMode": true,
    "LocalServiceUrl": "http://localhost:8000"
  }
```
Now your **appsettings.json** should look like:
![](/assets/images/2020-07-06/add-config.png)

 In the above code, we create configuration for DynamoDB service and setting the mode to local. Then, we set the service URL to `localhost` and port `8000` to run locally.
* Now we need to register Dynamo Service. In the `Startup.cs` file, add the below code in `ConfigureServices` method:
```
var dynamoDbConfig = Configuration.GetSection("DynamoDb");
var runLocalDynamoDb = dynamoDbConfig.GetValue<bool>("LocalMode");
services.AddSingleton<IAmazonDynamoDB>(sp =>
{
    var clientConfig = new AmazonDynamoDBConfig { ServiceURL = dynamoDbConfig.GetValue<string>("LocalServiceUrl") };
    return new AmazonDynamoDBClient(clientConfig);
});
```

**Code Explanation**

`var dynamoDbConfig = Configuration.GetSection("DynamoDb");` - Gets the section `DynamoDb` from the configuration file

`var runLocalDynamoDb = dynamoDbConfig.GetValue<bool>("LocalMode");` - Gets the value of local mode

`services.AddSingleton<IAmazonDynamoDB>` - Register a service `IAmazonDynamoDB` interface which returns the actual implementation. 

`var clientConfig = new AmazonDynamoDBConfig { ServiceURL = dynamoDbConfig.GetValue<string>("LocalServiceUrl") };` - Create an `AmazonDynamoDBConfig` instance passing in the `LocalServiceURL` from **appsetting.json**

`return new AmazonDynamoDBClient(clientConfig);` - Return the `AmazonDynamoDBClient` using the configuration.

### Read from DynamoDB Local

After registering IAmazonDynamoDB in our applicaiton we are now in the position to insert it using Microsoft Dependency injection in the our Controller. 

* Delete the two controllers, `S3ProxyController` and `ValuesController` in the controllers folder
* Right click on **Controllers** folder > **New C# Class**
* Name the class **CommentsController**
* Make sure the class is derived from `ControllerBase`
* Add the below line above the `class` keywords
```
[Route("getcomments")]
[ApiController]
```

**Code Explanation:**

`[Route("getcomments")]` -  Annotate the route where this controller can be accessed

`[ApiController]` -  Denotes that this is a controller

* Add the below code:
```
  private const string TableName = "commentsTable";
  private readonly IAmazonDynamoDB _amazonDynamoDb;

  public CommentsController(IAmazonDynamoDB amazonDynammoDb)
  {
      _amazonDynamoDb = amazonDynammoDb;
  }

  [HttpGet("{id}")]
  public async Task<ActionResult<string>> Get(int id)
  {
      var request = new GetItemRequest
      {
          TableName = TableName,
          Key = new Dictionary<string, AttributeValue>
          {
              {
                  "id",
                  new AttributeValue
                  {
                      S = id.ToString()
                  }
              }
          }
      };

      var response = await _amazonDynamoDb.GetItemAsync(request);

      return response.Item["username"].S;
  }
```

* Now insert a record in DynamoDB by running:
```
 aws dynamodb put-item --table-name commentsTable --item '{"id": {"S": "1"},"username": {"S": "zain"}}' --endpoint-url http://localhost:8000
```
* To check if the record has been inserted, scan tale using below command:
```
aws dynamodb scan --table-name commentsTable --endpoint-url http://localhost:8000
```
You should see response something like this:
<p align="center">
  <img src="/assets/images/2020-07-06/confirmation-of-insertion.png">
</p>

* Now run the application by running `dotnet run` in your project folder

* Click on the URL 

* After replacing `index.html` in with `swagger`, you should see the below page:
<p align="center">
  <img src="/assets/images/2020-07-06/swagger-final-page.png">
</p>
You are only seeing GET `request` as we have only implemented that method in our Controller.

* Click on the **GET** button in blue
<p align="center">
  <img src="/assets/images/2020-07-06/get-button.png">
</p>

* Click **Try It Out**
<p align="center">
  <img src="/assets/images/2020-07-06/try-it.png">
</p>

* Type `1` in the **id** text box
* Click **Execute**
<p align="center">
  <img src="/assets/images/2020-07-06/try-it-2.png">
</p>

* You should see the response `zain` in the **Responses** section
<p align="center">
  <img src="/assets/images/2020-07-06/try-it-result.png">
</p>

You have finally done it. Congratulation!!!! Stay tuned for other methods too.