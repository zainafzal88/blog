---
layout: post
title:  Build a Commenting System Using SAM in .NET
description: This is a walkthrough of how I built my commenting system for my blog with ASP.NET Core Web API and AWS DynamoDB using SAM
date:   2020-08-30
---
I wanted to create a commenting system for my blog instead of using an existing one in order to learn how it’s build which was the idea of my friend Paul.

## What's Covered
* Serverless Template
* Create the ASP.NET Core Web API project
* Integrating Swagger UI
* Connecting to AWS DynamoDB
* Getting all Comments from AWS DynamoDB By Post
* Inserting Comment into AWS DynamoDB
* Deleting Comment From DynamoDB

## Prerequisites:
* AWS account
* Knowledge of C#, Javascript, HTML and CSS
* .NET Core [SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1) and [Runtime](https://dotnet.microsoft.com/download/dotnet-core/3.1) installed (if you haven't got them, install them from the given links)
* .NET CLI

## Serverless Template
The below is the template code used to create all the resources needed
```
    {
    "AWSTemplateFormatVersion": "2010-09-09",
    "Transform": "AWS::Serverless-2016-10-31",
    "Description": "An AWS Serverless Application that uses the ASP.NET Core framework running in Amazon Lambda.",
    "Resources": {
        "AspNetCoreFunction": {
        "Type": "AWS::Serverless::Function",
        "Properties": {
            "Handler": "CommentsAPI::CommentsAPI.LambdaEntryPoint::FunctionHandlerAsync",
            "Runtime": "dotnetcore3.1",
            "CodeUri": "",
            "MemorySize": 256,
            "Timeout": 30,
            "Role": null,
            "Policies": [
            "AWSLambdaFullAccess",
            "AmazonDynamoDBFullAccess"
            ],
            "Events": {
            "ProxyResource": {
                "Type": "Api",
                "Properties": {
                "Path": "/{proxy+}",
                "Method": "ANY"
                }
            },
            "RootResource": {
                "Type": "Api",
                "Properties": {
                "Path": "/",
                "Method": "ANY"
                }
            }
            }
        }
        },
        "CommentsTable": {
        "Type": "AWS::DynamoDB::Table",
        "Properties": {
            "AttributeDefinitions": [
            {
                "AttributeName": "id",
                "AttributeType": "S"
            }
            ],
            "KeySchema" : [ 
            {
                "AttributeName": "id",
                "KeyType": "HASH"
            }
            ],
            "TableName":"comments",
            "ProvisionedThroughput": {
            "ReadCapacityUnits": "1",
            "WriteCapacityUnits": "1"
            }
        }
        }
    },
    "Outputs": {
        "ApiURL": {
        "Description": "API endpoint URL for Prod environment",
        "Value": {
            "Fn::Sub": "https://${ServerlessRestApi}.execute-api.${AWS::Region}.amazonaws.com/Prod/swagger"
        }
        }
    }
    }
```
## Create the ASP.NET Core Web API Project
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
  <img src="/assets/images/2020-08-30/project-folder-content.png">
</p>

  * `aws-lambda-tools-defaults.json` - This file is read by lambda tooling by default. Your function handler is specified in this file. So, if you update the function's name, you need to update it in this file too.
  * `LambdaEntryPoint` - Pretty much self explanatory (file from which lambda executes your application code) when you deploy to AWS
  * `LocalEntryProint`- Local testing entry point when you test the application locally
  * `serverless.template` - SAM template file in JSON which defines insfrastructure as code.

## Integrating Swagger UI

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
![](/assets/images/2020-08-30/swagger-config.png)

5. Run the application and then click the url shown `https://localhost:5001/`
6. Append `swagger` to the URL and press enter
7. If everything went well, you should see Swagger UI now
<p align="center">
  <img src="/assets/images/2020-08-30/swagger-ui.png">
</p>

## Connection to AWS DynamoDB
1. Navigate to you project folder. We will add two NuGet packages.These are essential to make a connection to DynamoDB
```
dotnet add package AWSSDK.DynamoDBv2
dotnet add package AWSSDK.Extensions.NETCore.Setup 
```
`AWSSDK.DynamoDBv2` - Add the sufficients support to interact with DynamoDB using AWS .NET SDK
`AWSSDK.Extensions.NETCore.Setup` - has methods for configuration and registrations of AWS Services with dependency injection.
2. Now we need to register Dynamo Service. In the `Startup.cs` file, add the below code in `ConfigureServices` method:
```
services.AddCors(c =>
{
    c.AddPolicy("AllowOrigin", options => options.AllowAnyOrigin());
});
services.AddAWSService<IAmazonDynamoDB>();
```
**Code Explanation**
* We add CORS policy as the origin we will be accessing the dynamoDb will be a different one. Hence, specifying `AllowAnyOrigin`
* `services.AddAWSService<IAmazonDynamoDB>();` - registering the Interface IAmazonDynamoDB

3. Delete the below line from the method:
```
services.AddAWSService<Amazon.S3.IAmazonS3>();
```
    
    Now, that we are connected, Let's read from database

    As we don't have anything to read from the database, first, we add a a record in the database manually to test our reading functionality and I am assuming you know how to add a record in the table. When you have written the record

## Getting all Comments from AWS DynamoDB (By Post)
In this section, we will get all the comments by Post. So, each post displays only comments that related to it

After registering IAmazonDynamoDB in our applicaiton we are now in the position to insert it using Microsoft Dependency injection in our Controller. 

1. Delete the two controllers, `S3ProxyController` and `ValuesController` in the controllers folder
2. Right click on **Controllers** folder > **New C# Class**
3. Name the class **CommentsController**
4. Make sure the class is derived from `ControllerBase`
5. Add the below line above the `class` keywords
```
[Route("api/[controller]")]
[ApiController]
[EnableCors("AllowOrigin")] 
```

If you get red squiffly lines, in the above three line, import what's needed

**Code Explanation:**

    `[Route("api/[controller]")]` -  Annotate the route where this controller can be accessed

    `[ApiController]` -  Denotes that this class is a controller

    `[EnableCors("AllowOrigin")]` - Enables Cross Origin Resources Sharing throughout the controller

6. Add below code inside the class:

```
    private const string TableName = "comments";
    private readonly IAmazonDynamoDB _amazonDynamoDb;

    public CommentsController(IAmazonDynamoDB amazonDynammoDb)
    {
        _amazonDynamoDb = amazonDynammoDb;
    }

    [HttpGet]
    public async Task<ScanResponse> GetComments()
    {
        var request = new ScanRequest
        {
                TableName = TableName
        };

        var response = await _amazonDynamoDb.ScanAsync(request);
        return response;
    }

    [HttpGet("{url}")]
    public async Task<ActionResult<string>> Get(string url)
    {
        var request = new GetItemRequest
        {
            TableName = TableName,
            Key = new Dictionary<string, AttributeValue>
            {
                {
                    "postId",
                    new AttributeValue
                    {
                        S = url
                    }
                }
            }
        };

        var response = await _amazonDynamoDb.GetItemAsync(request);

        return response.Item["username"].S;
    }
```
If you get red squiggly lines any where, just add all the imports needed.

## Inserting Comment into AWS DynamoDB
We will be creating a form which will ask for username and a comment. Then upon pressing submit button, it will write the data to DynamoDB.

1. In your frontend, create a form (copy paste the below code)
```
    <div class="comments-view">
        <h4>Please Share Thoughts</h4>
        <br/>
        <input id="name" type="name" class="form-control" placeholder="Enter your email">
        <br/>
        <textarea id="comment" class="form-control" placeholder="Share thoughts here"></textarea>
        <br/>
        <button id="submit" class="btn btn-primary" >Comment</button>
        <br/>
        <br/>
        <div id="all-comments" class="show-comments"><!--Comments populated by script above--></div>
    </div>
```
2. Add the below javascript / Ajax code to communicate to ASP.NET Core Web API
```
  <script>
  $(function(){

    let allComments =  document.getElementById("all-comments");
    let $name = $("#name");
    let $comment = $("#comment");

    $("#submit").on('click', function(){
        
        //validation to prevent html tags being inputted input box
        var reg =/<(.|\n)*?>/g;

        if (reg.test($('#comment').val()) == true) {
            var errorText ='HTML Tags Not Allowed';
            alert(errorText);
        }
        else  
        {
            var item =
            {
            username: $name.val(),
            comment: $comment.val(),
            postId: window.location.pathname,
            };

            $.ajax({
            type: "POST",
            url: "[your-api-url-here]]",
            data: JSON.stringify(item),
            contentType: "application/json",
            success : function(){
                alert("Comment submitted successfully")
                location.reload();
            },
            error : function(err){
                alert("error in post" + JSON.stringify(err))
            }
            });
        }

        $name.val("");
        $comment.val("");
    });
  });
</script>
```
That is all we needed to do in the frontend.

3. Now to back to your ASP.NET Core Web API project
4. Create a folder called `Model` and in it, create new C# class called `Comments`
5. Add the below code in it
```
namespace CommentsAPI.Models
{
    public class Comments
    {
        public string Username { get; set; }

        public string Comment { get; set; }
        
        public string PostId { get; set; }
    }
}
```
4. Navigate to the **CommentsController** and add the below code:

```
    [HttpPost]
    public async Task Post([FromBody] Comments comment)
    {
        Guid uuid = Guid.NewGuid();
        var item = new Dictionary<string, AttributeValue>()
        {
            {"id", new AttributeValue{S = uuid.ToString()}},
            {"username", new AttributeValue{S = comment.Username}},
            {"comment", new AttributeValue{S = comment.Comment}},
            {"postId", new AttributeValue{S = comment.PostId}},
            {"date", new AttributeValue{S = DateTime.Now.ToString()}}
        };

        PutItemRequest request = new PutItemRequest
        {
            TableName = TableName,
            Item = item
        };
        await _amazonDynamoDb.PutItemAsync(request);
    }
```
**Code Explanation**

`[HttpPost]` - Annotation that the this method handles POST requests only

`[FromBody]` - Annotate thats take all the content from the body

The rest logic is just taking all the user inputs and inserting them in relevated column in the database asynchronously

## Deleting Comment From DynamoDB
This can be done in two ways
1. Manually by logging into AWS and deleting the record from the DynamoDB Table
2. Click a button from the frontend.

As a blog owner, I believe users shouldn't have the ability to delete comments. Hence, I didn't implement this, However, if you want to implement it, please copy paste the below code in the `CommentsController` and do an AJAX request from the frontend on the click of a button provide the comment that need to be deleted is selected.
```
    [HttpDelete("{id}")]
    public async Task Delete(string id)
    {
        var request = new DeleteItemRequest
        {
            TableName = TableName,
            Key = new Dictionary<string, AttributeValue>()
            {
                {"id", new AttributeValue{S = id}}
            }
        };
        
        await _amazonDynamoDb.DeleteItemAsync(request);
    }
```
Congratulations!!! You have sucessfully implemented your own commenting system for you website / blog etc. However, if any of the above didn't work for you, please leave a comment below and i'll contact you via email