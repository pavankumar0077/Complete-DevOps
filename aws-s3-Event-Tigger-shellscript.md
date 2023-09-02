![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/87d46585-c10f-441c-ac58-3d1123bfbf90)

Ex: 1) **User** uploads this new content onto Netflix and how all the users of Netflix whenever you open Netflix 
you get information of what is newly uploaded to Netflix.
2) most of them use **S3 buckets** for storing because S3 is the cheapest service that is 
available and you know in terms of Netflix.
3) S3 bucket triggers a **lambda function** why a Lambda function why not ec2 instance
4) Because Lambda is a serverless computer, so not like your EC2 instance which 
always stays there Lambda is a **serverless compute** that is only triggered like when you use
get charged for the time.Always preferred to use Lambda
5) After lambda information is sent to **SNS**,SNS is a simple notification service
6) **SNS** it depends on platform to platform like you know if this is just a Blog posting website like for example a 
medium or email notification
7) why we are going to use **IAM** because Lambda function needs to have permission to be invoked by 
S3 bucket and also to invoke SNS right because Lambda has to do all of these things 
there has to be some IM permissions that will have to Grant to Lambda.
8) Lambda function is creating only when event is triggered

ALL FIELS ARE AVAIABLE IN THIS REPO ``` https://github.com/pavankumar0077/shell-scripting-projects ```

