# DynamoDb-Stream-Data-Capture
Using DynamoDb Stream to capture item-level changes in the DynamoDb table, then use kinesis data stream and kinesis firehose to save the changes into a S3 bucket. A lambda function will be use to transform the data before dumping it into S3.
