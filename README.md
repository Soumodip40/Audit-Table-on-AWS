# Audit-Table-on-AWS
Audit Table in AWS
The aim of this project is to make an audit table in the context of DynamoDB, which helps us to track changes on records in our database. They are an important tool in our toolbelt to understand the stateful transitions that our records go through. This is important for auditing, debugging, and general understanding of how clients are modifying our records. Here in this project I have created an architecture to set one up with the help of DynamoDB, DynamoDB Streams, Lambda, Kinesis Data Firehose, S3, and Amazon Athena.

Application architecture
application-architecture

From this project the key things I have learnt, how to migrate data from a DynamoDB table into a cold store in S3, and perform SQL queries on it using Athena.

This project is divided into five parts. Each part describes a scenario of what I'm going to build and step-by-step directions to implement the architecture.

1. Create a DynamoDB Table

This DynamoDB table will hold our 'Orders' information and stores those states of your orders that we want to audit later.

2. Create a Lambda Function

This Lambda function will process those orders information from DynamoDB and push it to Kinesis Data Firehose.

3. Create the DynamoDB Streams Trigger

Turning on DynamoDB Streams will enable us to get all the events of updation happened on any table item. So, when any order state gets changed, we can have a track later on. This stream will push those info to lambda for further processing.

4. Create a Kinesis Data Firehose stream and a S3 Bucket

Kinesis Data Firehose will put those informations through batches into an S3 bucket.

5. Retrieve Audit Insights using Amazon Athena

Using Athena we will run SQL queries onto that S3 bucket to get our insights regarding Orders.

Part 1: Create a DynamoDB Table
Here in this step, the part of the application architecture we focused shown below:

part-1

Go to the DynamoDB console.
Start Create table, give a Table name and a Partition key and keep everything default, then Create table.
1 2

Part 2: Create a Lambda Function
Here in this step, the part of the application architecture we focused shown below:

part-2

Go to the Lambda console.
Start Create a function, choose Author from scratch and give a Function name, select a latest Python Runtime.
3

Now for the Lambda execution role, we need to create a IAM Role that will have enough permissions for our purpose. Follow the below steps:
IAM Roles and Permission Policies needed to be attached with the lambda function:

AmazonKinesisFirehoseFullAccess
AWSLambdaBasicExecutionRole
DynamoDB: GetRecords
DynamoDB: DescribeStream
DynamoDB: GetShardIterator
DynamoDB: ListStreams
Go to IAM console and start Create role, Select trusted entity as AWS service and the service as Lambda then Next.
4

Find and attach all the permissions mentioned above and then Next. Give a suitable Role name, review everything and Create role.
5 6 7

Now we need to give this role to our lambda, so go back to that lambda console and expand Change default execution role, choose to Use an existing role and select that role that we just created form the dropdown. Finish the Create function.
8

Now update the code here and Deploy. Also don't forget to change the DeliveryStreamName with the Kinesis Data Firehose name that will create later or you can comeback later after creating the Kinesis Data Firehose and update the code.
9

Part 3: Create the DynamoDB Streams Trigger
Here in this step, the part of the application architecture we focused shown below:

part-3

Go back to the Orders table page in the DynamoDB console.
Change the tab to Exports and streams, scroll below and come to DynamoDB stream details to Turn on.
10

Click on Turn on, choose New image as we want to only get the latest or updated information not the old one. Then Turn on stream.
11 12

It should show the stream status as On and now triggers are ready to be created. So Create trigger, select the Lambda function that we created and choose a preferred Batch size.
13 14

Part 4: Create a Kinesis Data Firehose stream and a S3 Bucket
Here in this step, the part of the application architecture we focused shown below:

part-4

Go to the Amazon S3 console and create a bucket by putting a globally unique bucket name and keep rest of everything as default.
This will be the bucket where Kinesis Data Firehose will put records to.

15

Go to the Amazon Kinesis Data Firehose console and Create delivery stream, choose the source as Direct PUT and the destination as Amazon S3. Give a delivery stream name (this name should be the same name as written inside the lambda code or this name needs to be updated in the lambda code). Now it will ask for the S3 bucket, here need to select the bucket just created.
16 17 18 19

Part 5: Retrieve Audit Insights using Amazon Athena
Here in this step, the part of the application architecture we focused shown below:

part-5

Before proceed with this part, we need to add some items inside our DynamoDB table so that we can validate. So to do that, go to that Orders table page > Explore table items > Create item > then add item values that you want to add or you can use JSON.
