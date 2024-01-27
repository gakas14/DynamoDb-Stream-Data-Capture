# Streaming Amazon DynamoDb data into a centralized data lake (S3)
DynamoDb Stream captures item-level changes in the DynamoDb table. Then, Kinesis Data Stream and Firehose save the changes to an S3 bucket. A lambda function transforms the data before dumping it into S3.

![dynamodb_Stream](https://github.com/gakas14/DynamoDb-Stream-Data-Capture/assets/74584964/b5d05783-059c-4ab4-b30d-aba9f430c6e2)

## Step 1: Create a DynamoDB table. 

Create a customer table with a customer_id as the primary key,
<img width="908" alt="Screen Shot 2024-01-27 at 2 28 39 PM" src="https://github.com/gakas14/DynamoDb-Stream-Data-Capture/assets/74584964/8deccb12-f0de-4b1f-a4fd-3cec29df0da1">

 

## Step 2: Create a kinesis data stream 
<img width="972" alt="Screen Shot 2024-01-27 at 2 32 59 PM" src="https://github.com/gakas14/DynamoDb-Stream-Data-Capture/assets/74584964/2db60ef1-0d22-45a0-9fb3-b53d8ab0faff">



## Step 3: Create a lambda function and a s3 bucket 
<img width="1245" alt="Screen Shot 2024-01-27 at 2 40 26 PM" src="https://github.com/gakas14/DynamoDb-Stream-Data-Capture/assets/74584964/fd59e29c-5677-4f46-825c-e709fbe61d61">

#### Lambda function script
```
          # This function adds a new line in between each record coming from Kenesis data stream 
          import json
          import boto3
          import base64
          output = []
          
          def lambda_handler(event, context):
              print(event)
              for record in event['records']:
                  payload = base64.b64decode(record['data']).decode('utf-8')
                  print('payload:', payload)
                  
                  row_w_newline = payload + "\n"
                  print('row_w_newline type:', type(row_w_newline))
                  row_w_newline = base64.b64encode(row_w_newline.encode('utf-8'))
                  
                  output_record = {
                      'recordId': record['recordId'],
                      'result': 'Ok',
                      'data': row_w_newline
                  }
                  output.append(output_record)
          
              print('Processed {} records.'.format(len(event['records'])))
              
              return {'records': output}

```


 

## Step 4: Create the kinesis firehouse 
<img width="1259" alt="Screen Shot 2024-01-27 at 2 38 12 PM" src="https://github.com/gakas14/DynamoDb-Stream-Data-Capture/assets/74584964/c94c6d82-f5c7-4f93-91b8-b10bcd4c5b48">


#### With source as kinesis data stream and destination as s3 

#### Add Transform source records with AWS Lambda. 

#### Add the s3 bucket 

#### Configure the Buffer size and interval to dump the data only if the size is one MB or in 60 seconds. 

<img width="1257" alt="Screen Shot 2024-01-27 at 2 47 53 PM" src="https://github.com/gakas14/DynamoDb-Stream-Data-Capture/assets/74584964/3d4b7d22-0291-42ea-8611-516c5627a7da">

## Step 5: setup the DynamoDB stream with the kinesis  

##### Turn on the Amazon Kinesis data stream from the DynamoDB table. 
<img width="1254" alt="Screen Shot 2024-01-27 at 2 50 45 PM" src="https://github.com/gakas14/DynamoDb-Stream-Data-Capture/assets/74584964/73b22c35-d7e7-470e-bb21-07d90f527960">



## Step 6: insert data into the table  
<img width="1251" alt="Screen Shot 2024-01-27 at 3 08 35 PM" src="https://github.com/gakas14/DynamoDb-Stream-Data-Capture/assets/74584964/ffe18c56-8113-48f4-89ab-921cb549c83f">

#### SQL Queriew for DynamoDB
```
INSERT INTO "customers" value {'customers_id':1, 'name':'Baba Li', 'age':20,'gender':'M'}
INSERT INTO "customers" value {'customers_id':2, 'name':'Lucky Bill', 'age':24,'gender':'M'}
INSERT INTO "customers" value {'customers_id':3, 'name':'Mom Ma', 'age':50,'gender':'F'}
INSERT INTO "customers" value {'customers_id':4, 'name':'Locker Su', 'age':30,'gender':'M'}
INSERT INTO "customers" value {'customers_id':5, 'name':'Abdel ly', 'age':41,'gender':'F'}
INSERT INTO "customers" value {'customers_id':6, 'name':'Abou Sar', 'age':35,'gender':'F'}

update customers set age=26 where customers_id=3

select * from customers;
```

<img width="1259" alt="Screen Shot 2024-01-27 at 3 08 42 PM" src="https://github.com/gakas14/DynamoDb-Stream-Data-Capture/assets/74584964/dae2c050-a099-43e3-bcc2-5f0829fe754b">

## Step 7: Check the data in the s3 bucket
<img width="1264" alt="Screen Shot 2024-01-27 at 3 11 05 PM" src="https://github.com/gakas14/DynamoDb-Stream-Data-Capture/assets/74584964/d924480a-da00-44f3-a35d-c24d61493d84">
