---
layout: post
title: Scrumblr on LocalStack (S3 + SAM) 
description: How to run Scrumblr (development mode) on LocalStack 
date:   2021-05-10
---
In this post, I will be showing you how to setup Scrumblr project with DynamoDB, S3 and SAM in LocalStack

### Pre-requisites 
* LocalStack (run `pip install localstack`) installed
* Python 3.6 or 3.7 installed
* [AWS CLI] (Follow instructions [here](https://github.com/aws/aws-cli) to install it)
* [Docker](https://docs.docker.com/get-docker/)
* SAM CLI (following the instructions [here](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/serverless-sam-cli-install-mac.html) to install it)
* [NoSQL Workbench](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/workbench.settingup.html)
* VS Code
* Postman (For API testing)
* Clone the [repository](https://github.com/zainafzal88/scrumblr-enhancement)

### Assumptions
* You know who to create a new SAM application and run it successfully locally.

#### Step 1 
Start docker.

#### Step 2
Clone the repository form [here](https://github.com/zainafzal88/scrumblr-enhancement)

#### Step 3
Open the repository in VS Code

#### Step 4
Run the below commands on by one
```
cd scrumblr-enhancement
cd backend
```
#### Step 5
Go to VS Code's terminal and navigate to `scrumblr-api` folder

#### Step 6
Run the command `docker-compose up` to start LocalStack in docker(ensure docker is already running). This command will start all the services defined in `docker-compose.yaml`
If the stack is successfully created, you will see the message at the end
```
2021-05-08T12:43:43:INFO:localstack.utils.analytics.profiler: Execution of "start_api_services" took 10920.12095451355ms
```

#### Step 7
Open another terminal by clicking the `+` icon in the VS Code terminal window and navigate to `backend > scrumblr-api` folder

#### Step 8
Run `sam build` in the folder where `template.yaml` file is located (this will be in the same folder as `docker-compose up` file). Ensure your sam build is successful.

#### Step 9
Open up NoSQL workbench and create a connection to localstack. See below steps on how to create a connection

*   Click on **Operation Builder**
*   Click **Add Connection**
*   Click on **DynamoDB Local**
*   Give connection a name
*   Enter `4566` for **Port**
*   Ensure Docker is running
*   Click **Connect**
*   You will be taken to the **Operation builder** window within NoSQL Workbench
*   Search for the connection you just created and click **Open**

Now your connection should be up and ready

#### Step 10
Now go back in the terminal where sam build was successfull and create a dynamodb table using the below command:
```
aws dynamodb create-table \
    --table-name DevCop \
    --attribute-definitions \
        AttributeName=BoardId,AttributeType=S \
        AttributeName=NoteId,AttributeType=S \
    --key-schema \
        AttributeName=BoardId,KeyType=HASH \
        AttributeName=NoteId,KeyType=RANGE \
--provisioned-throughput \
        ReadCapacityUnits=10,WriteCapacityUnits=5 \
--endpoint-url=http://localhost:4566
```

The above commmand will create table named `DevCop` in your localstack

#### Step 11
To verify table has been created, go to NoSQL Workbench > Operation builder > Open the connection you previously created for localstack.

#### Step 12
Once it's created, it will appear in your connection (go the Operation Builder > choose your connection name from the dropdown) in NoSQL Workbench and click refresh icon next to **Tables**.

#### Step 13
Let's insert a record in the newly created table. For testing purposes, we will insert some hardcoded values which are already there in the `app.js` file in the method `POST`. To perform an insert do the following.

*   Make sure localstack is running (run the command `docker-compose up` to start localStack if it not yet running)
*   In `app.js` modify the `POST` method with the below code
```
app.post("/", async(req, res) => {

    var params = {
        TableName: table,
        Item : {
            BoardId : uuidv4(),
            NoteId: uuidv4(),
            datetime: '26/02/2021', // replace with date and time object
            BoardName: "devcop",    // replace with board name in the url path (coming from the frontend)
            Note : "This is my test note 1"   // replace with note(text) coming from the frontend
        }
    }
```
*   Delete the folder `.aws-sam` folder and run `sam build` in `scrumblr-api` folder
*   Make sure project has been build successfully
*   Start SAM app using `sam local start-api` command (this will run sam app locally)
*   Open Postman and insert the URL in the address bar of Postman
*   Replace the url with `http://localhost:3000/`
*   Change the request to **POST** from the dropdown next to the address bar
*   Click **Send**
*   After few second you should see a message, *Inserted Successfully* in the **Body**

#### Step 14
To verify the insert has been successful,

*   Go to NoSQL Workbench
*   Go to **Operation builder**
*   Click **Open** for the connection you created
*   Click on the table you created
*   The record should appear there. If it doesn't try to refresh(two circling arrown where it says **Displaying [a number] Items**) it the table.

#### Step 15
If you want to test `GET` request, simply change the request method in Postman to `GET` from the dropdown, click **Send** and all the records in the table should appear in **Body** tab
 
#### Step 16
To put front end file on LocalStack, create a bucket in localstack using the command below
```
aws s3 mb --endpoint-url=http://localhost:4566 s3://[replace-this-with-your-bucket-name]
```

You should get a response saying make_bucket: [whatever-you-named-your-bucket]

#### Step 17
Upload all the files from the client folder to the S3 bucket you just created
*   Navigate to `client` folder from the terminal (type `cd ..` and Enter twice)
*   cd into `client` folder (this folder contain all the frontend files)
*   Run the command `aws --endpoint-url=http://localhost:4566 s3 --recursive cp ./ s3://[whatever-you-named-your-bucket]`
*   Press `Enter`

#### Step 18
Go to your browser and type `http://localhost:4566/[your-bucket-name]/home.html`. You should beable to see your `home.html` page in the browser now.

I'll be very happy to hear what you think or suggest any improvements.