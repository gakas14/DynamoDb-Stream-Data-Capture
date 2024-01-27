# Streaming Amazon DynamoDb data into a centralized data lake (S3)
DynamoDb Stream captures item-level changes in the DynamoDb table. Then, Kinesis Data Stream and Firehose save the changes to an S3 bucket. A lambda function transforms the data before dumping it into S3.
