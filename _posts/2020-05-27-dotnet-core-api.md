---
layout: post
title:  "Basic ASP.NET Core Web API Connected To PostgreSQL via Docker"
description: This is a guide to create a ASP.NET Core Web API using Visual Studio and connect it to PostgreSQL database using Docker 
date:   2020-05-27
---
In order to brush up on my .NET skills, I didn’t know where to start. My friend, Paul, asked me to make build a .NET Core Web API and connect it to PostgreSQL database. It sounded fun for two reasons...

* It’s programming (obviously)

* I never worked with PostgresSQL so, an opportunity to learn a new database

Let’s begin!!

##   What you will need

* Visual Studio For Mac (VS)

* DBeaver

* Docker

* Docker-Compose

Let the exciting journey begin

##   Create docker-compose.yml

If you don’t know, `docker-compose.yml` is a configuration file for Docker-Compose (tool for defining and running multiple containers) used to configure application’s services.

**_NOTE: This file must be in the root of your project, otherwise, it won’t work._**

Create a file and name it `docker-compose.yml` and add the below code in it

```
version: '3'
services:
  postgres:
    image: postgres:latest
    ports: 
      - "5432:5432"
    container_name: database
    environment:
      - POSTGRES_PASSWORD=password
```

**Code Explanation:**

`version` - which version is used

`services` - what services we are running

`postgres` - service name

`image` - name of the image to be used

`ports` - which port(s) to be used

`container_name` - name of the container

`environemnt` - consist of environment variables

 

##  Create a new Project

1.  Open **Visual Studio**

2.  Click **New**

3.  Under **Web and Console**, click **App**

4.  Under **ASP.NET Core**, select **API**

5.  Click **Next**

6.  Ensure **.NET Core 3.1**. is select for **Target Framework**

7.  Give project a name like **YourProjectName**

8.  Click **Create**

9.  Your project folder structure should look like the below:

_Note: If you can’t see the view **Solution** View, go to **View** on the menubar and select **Design**._

<p align="center">
  <img src="/assets/images/2020-05-27/dotnet1.png">
</p>

##   Create A Model

A model represent the data to be managed for the application. Our model will be call UserInfo because we are saving users' information in it.

1.  Right click on **YourProjectName** in the **Solution Explorer** →  **Add** → **New Folder**

2.  Name it as **Models**

3.  Right click on **Models** > **Add** > **New Class**

4.  Name it as **UserInfo**

5.  Add the below code
```
public class UserInfo
    {
        public long id { get; set; }
        public string email { get; set; }
        public string firstName { get; set; }
        public string lastName { get; set; }
        public string occupation { get; set; }
        public string title { get; set; }
        public string url_Blogs { get; set; }
        public string url_Github { get; set; }
        public string url_Linked { get; set; }

    }
```
The classes in the Model’s folder can be placed anywhere in the project, however, the structure above is used as best practice to keep project organised and structured

##   Create A Database Context

This is the class which is an a vital part of Entity Framework. It inherits from DbContext which represents a session with the database and then can be used as querying and saving to the database

1.  Right click on **Models** > **Add** > **New Class**

2.  Name is as `UserInfoDbConext`

3.  import `Microsoft.EntityFrameworkCor`

4.  Derive it from `DbContext`

5.  Add the below code:

```
using Microsoft.EntityFrameworkCore;

namespace YourProjecName.Models
{
    public class UserInfoContext : DbContext
    {
        public UserInfoContext(DbContextOptions<UserInfoContext> options)
            : base(options)
        {
        }

        public DbSet<UserInfo> UserInfo { get; set; }

    }
}
```

##   Register The Above Database Context

For the database context we just created, it needs to be registered with the Dependency Injection container for it work. We will also be connecting to PostgreSQL database using a connection string.

1.  Open **Startup.cs** (should be in the project’s root)

2.  Add the code (shown by prefixing and suffixing ***)

```
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
***using Microsoft.EntityFrameworkCore;***
***using YourProjectName.Models;***

namespace YourProjectName
{
    public class Startup
    {
        public Startup(IConfiguration configuration)
        {
            Configuration = configuration;
        }

        public IConfiguration Configuration { get; }

        // This method gets called by the runtime. Use this method to add services to the container.
        public void ConfigureServices(IServiceCollection services)
        {
            ***services.AddDbContext<UserInfoContext>(options =>
                 options.UseNpgsql(Configuration.GetConnectionString("DefaultConnection")));***
            services.AddControllers();
        }

        // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
        public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
        {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }

            app.UseHttpsRedirection();

            app.UseRouting();

            app.UseAuthorization();

            app.UseEndpoints(endpoints =>
            {
                endpoints.MapControllers();
            });
        }
    }
}
```
**Code Explanation**

Specifies to use the connection string from the config to connect to PostgreSQL database. We haven’t added the connection string in the config yet. That’s what we will do now.

##   Adding Database Connection String

This string will specify the credentials and details of PostgreSQL database for the application to connect to.

 Open `appsettings.json` and the below JSON under `AllowedHosts: ”*”`
```
 "ConnectionStrings": {
    "DefaultConnection": "Host=localhost;Port=5432;Username=postgres;Password=password;Database=postgres;"
  }
```
Make sure the connection details match your `docker-compose.yml`

**_ALERT - In production you should NEVER hardcode sensitive information in the files. Instead they should be encapsulated in environment variables_**

##   Scaffolding Controller

1.  Add the following NuGet packages in your project
```
dotnet add package Npgsql.EntityFrameworkCore.PostgreSQL
dotnet add package Npgsql.EntityFrameworkCore.PostgreSQL.Design
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
```
`dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design` - Installs the scaffolding (code generation of CRUD operations) engine

2.  Create a controller 

*   Right clicking **Project** itself in the **Solution Explorer**

*   **Add** > **New Scaffolding** > Click **Next**

*   **Model class** to be used would be **UserInfo** (if you have been following from the start)

*   **DbContext class** to be used would be **UserInfoDbContext** (if you have been following from the start)

*   Give controller a name **UserInfoController** > click **Finish**

<p align="center">
  <img src="/assets/images/2020-05-27/dotnet3.png">
</p>

After a few moment, you should see your controller with CRUD (create, remove, update, delete) operation in the **Controllers** folders

##   Customise GET and POST Methods

1.  Open the new created controller, **UserInfoController** in **Controllers** folder
2.  Replace the exisiting code with the below:

```
using System.Collections.Generic;
using System.Linq;
using Microsoft.AspNetCore.Mvc;
using RoarcoderUserInfoApi.Models;

namespace RoarcoderUserInfoApi.Controllers
{
    [Route("api/[controller]")]
    [ApiController]
    public class UserInfoController : ControllerBase
    {
        private readonly UserInfoContext _context;

        public UserInfoController(UserInfoContext context)
        {
            _context = context;
        }

        // GET: api/UserInfo
        [HttpGet]
        public ActionResult<List<UserInfo>> GetAll()
        {
            return _context.UserInfo.ToList();
        }

        // GET: api/UserInfo/5
        [HttpGet("{id}")]
        public ActionResult<UserInfo> GetUserInfo(long id)
        {
            var userInfo = _context.UserInfo.Find(id);

            if (userInfo == null)
            {
                return NotFound();
            }

            return userInfo;
        }

        [HttpPost]
        public void Post([FromBody] UserInfo userInfo)
        {
            _context.Add(userInfo);

            _context.SaveChanges();
        }
    }
}
```
**Code Explanation**

**Line 8:**

`[Route("api/[controller]")]` - specifies the route for the controller. That means when you want to access the controller from the browser, you will suffix `api/the-controller-name` to your localhost and port number. 

For example: `https://localhost:5000/api/userinfo` 

**Line 9**

`[ApiController]` - attribute means that the controller listens to web api requests

**Line 20 to 24**

`[HttpGet]` - attribute means when the get request is sent by client, this attributes receives it and invokes `GET` method. 

This methods get all the record from the database table as a list and display it

**Line 27 to 38**

`[Http({“id“})]` - attribute means that when a user request is sent with an `id` (a unique identifier), this method is invoked and the id from the url is passed into it.

This method takes in an `id` and returns a single record corresponding to that id.

**Line 40 to 46**

`[HttpPost]` - attribute means when the post request is sent by client, this attributes receives it and invoke POST method

`[FromBody]` -  means to take the content to be posted from the body

This methods take the content form the body and inserts in the database

##   Add EF Migrations

Run the below command in the project folder in CLI to add Entity Framework Migration 

```
dotnet ef migrations add InitialMigration
```
<p align="center">
  <img src="/assets/images/2020-05-27/dotnet5.png">
</p>
This generates the code that create the database from scratch. The generated file is named as **<timestamp>_InitialMigration** and is located in **Migrations** folder.

It contains of two methods:

`Up` - Create the database table(s) mentioned in the DbContext  and columns from the model class UserInfo

`Down` - Deletes what’s created

##   Updating Database

```
dotnet ef database update
```
<p align="center">
  <img src="/assets/images/2020-05-27/dotnet6.png">
</p>

This updates the database to the latest or particular migrations

##   Connecting to Database with PostgreSQL DB Management Tool

1.  Open PostgresSQL database management tool → Select **PostgreSQL** → Click **Next**

<p align="center">
  <img src="/assets/images/2020-05-27/dotnet9.png">
</p>

2.  Fill in the details appropriately

3.  If all the details were correct, you should see **Connected**  upon clicking **est Connection**.

4.  Click **OK** > **Finish**

<p align="center">
  <img src="/assets/images/2020-05-27/dotnet10.png">
</p>

5.  Insert a record in your database table

_Please note: If your table name consists on mixed of upper and lower case letter, ensure to include quotesaround, otherwise, PostgreSQL will consider it lowercase by default and it won’t work_

##   Testing API Using Postman

Open terminal and go to your project’s folder and run 
```docker-compose up 
```
The above command starts all docker services.

Build and run your project in VS

Open Post man and do the following:

**GET Request:**

<p align="center">
  <img src="/assets/images/2020-05-27/dotnet7.png">
</p>

1 - URL where application is listening

2 - Type of Request

3 - Send the request (moment of truth)

4 - You should get 200 OK in response

5 - The data from the database is shown

Post Request:

<p align="center">
  <img src="/assets/images/2020-05-27/dotnet11.png">
</p>

1 - URL where application is listening

2 - Type of Request

3 - Switch to body

4 - Enter the content in JSON you want to insert in the database 

5 - Send the request (moment of truth)

6 - You should get 200 OK in response

Now to confirm, go to the PostgreSQL DB Tool and check

As you can see, we already had one record in the table. We inserted  one more (id=2) and it’s inserted successfully.

<p align="center">
  <img src="/assets/images/2020-05-27/dotnet12.png">
</p>

Hope, it's working for you as well. If it’s not, feel free to get in touch and i’ll help you.

