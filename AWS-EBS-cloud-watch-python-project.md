# PROJECT DESCRIPTION

# As a Cloud Engineering team we take care of the AWS environment and make sure it is in compliance with the organizational policies.
We use AWS cloud watch in combination with AWS Lambda to govern the resources according to the policies.
For example, we Trigger a Lambda function when an Amazon Elastic Block Store (EBS) volume is created. We use Amazon CloudWatch
Events. CloudWatch Events that allows us to monitor and respond to EBS volumes that are of type GP2 and convert them to type GP3.

# Create Lambda function
1) AWS console --> Lambda function
2) name ebs_volume_check
3) Run time python 3.10
4) create --> rest settings are same
5) click on test --> create event and check whether it is working or not.

# Create Cloud Watch
1) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/6cb939f4-c1e2-4bde-a040-1ed25463fe3e)
2) Event source ==  AWS events or EventBridge partner events
3) Sample event - optional == AWS Event
4) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/d27ef430-ce45-420e-a9a4-9c8965d71d25)
5) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/e08ff7f7-f859-4129-9f6f-46989f90f4c5)
6) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/938eb545-fbe4-4cab-87a7-2cfa9ed9875e)
7) next -- next -- create
8) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/ace6d93a-9299-49b2-8aed-9e2fdcc6d6ff)
9) Verify whether cloud watch trigger the lambda function

```
import json

import json

def lambda_handler(event, context):
    # TODO implement
    
    print(event)
    
    return {
        'statusCode': 200,
        'body': json.dumps('Hello from Lambda!')
    }

```
10) The above code is by default given by aws. we can change the lambda_handler in the configuration we can provide any custom name Ex: def ebs_volume_handler
11) event and context are provided by the invoking resource,Now cloudwatch is invoking the lambda function the event is the cloud watch event -- event is basically a JSON is provided by cloud watch
12) print(evet) is only for testing
13) Very time if we made any changes click on deploy or ctrl + s and test it
14) delete and create volume again to check
15) Check cloudwatch -- loggorups -- with new logs find version 0 , id and etc details like this
```
{
   "version":"0",
   "id":"ad65d234-56c2-9cb6-ef32-99af2a36d8f3",
   "detail-type":"EBS Volume Notification",
   "source":"aws.ec2",
   "account":"794982227033",
   "time":"2023-09-24T17:03:35Z",
   "region":"us-west-1",
   "resources":[
      "arn:aws:ec2:us-west-1:794982227033:volume/vol-03a902d6055b74ef2"
   ],
   "detail":{
      "result":"available",
      "cause":"",
      "event":"createVolume",
      "request-id":"beae21b5-cca6-4b6b-88e6-e9f70e0c9c40"
   }
```
16) IAM --> Roles --
17) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/317fb32b-2c9e-4460-9e1e-b2a789580f54)
18) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/7648f2e5-bae8-44a6-b059-0ee24cceeef6)  click on it and create inline policy
19) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/14b1998d-9b5c-461a-8c57-5fbd03eb3417)
20) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/45b2d7b8-2d8f-4b2b-b523-c9afcf5f6d6c)
21) Delete and re-create the volume
22) Now the TYPE GP2 will be Changed to GP3

Final Code
--
1) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/1ba1d226-aa45-4532-9c6d-c0f87d31ca0e)
```
import boto3

def get_volume_id_from_arn(volume_arn):
    # Split the ARN using the colon (':') separator
    arn_parts  = volume_arn.split(':')
    
    #The volume ID is the last part of the ARN after the 'volume/' prefix
    volume_id = arn_parts[-1].split('/')[-1]
    return volume_id
    
    
    

def lambda_handler(event, context):
    
    volume_arn = event['resources'][0]
    volume_id = get_volume_id_from_arn(volume_arn)
    
    # We can use different different clients for different services like ec2, s3 and etc services 
    ec2_client = boto3.client('ec2')
    
    response = ec2_client.modify_volume(
        VolumeId=volume_id,
        VolumeType='gp3',
    )
```

REF BOTO3 DOCS : https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/ec2/client/modify_volume.html




