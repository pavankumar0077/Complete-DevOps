# AWS CLOUD WATCH

REF LINK : https://github.com/pavankumar0077/aws-devops-zero-to-hero/tree/main/day-16

Watching the activies that are used by AWS CLOUD (Like GateKeeper for AWS)
It is basically a Gate keeper for AWS, Which will help in understanding and implementing the
- Monitoring
- Alerting
- Reporting
- Logging
  
Using these activites are tracked

Monitoring
--
- Monitoring is a very important activity for devops engineer, For kubernetes applications monitoring, Infra monitoring 

Real-life metrics *
--
- Metrics are like how many API requests that EC2 instance received
- CPU or memory consumption of the EC2 instance

Alarms
--
- Collected a metrics for CPU utilization 80%, 90% and .... we need to take actions if it more than the threshold value
- Send an alarm if it is 90% used

Log insights
--
- This specific service try to access your EC2 instances, or S3 like that
- capability of providing you insights of which service is using the other service

Custom Metrics *
--
- CPU utilization (Default metric)
- Cloudwatch does not track -- Memory Usage
- In this case we can send Custom metrics for memory Usage

Cost Optimization *
--
- Cloudwatch play imp roles in the cost optimization
- Scaling

# -------------------------------------------------------------
Logs
--
1) For logs we have Log groups -- AWS automatically store the logs in the log group specific to that service
2) Logs insights -- We can query the logs and get the logs

Metrics
--
1) Cloudwatch is contantly collects the data from all the services 24/7
2)![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/f3fe7a2a-5940-460d-8c81-6b7b04bf860b)
3) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/770147b0-7e2a-46c7-be42-3c809386f8c6)


How to create an alarm on specific uasge
--
1) Search for alaram --> select metrics
2) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/f709d18c-c9a1-4ba3-a105-f43f8898b4b8)
3) Select with instance id or name --> specific metrics --> Click on select
4) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/271b191c-14b1-481e-b971-73bf7bc03bf7)
5) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/583e2dc5-ec22-4259-85ae-12df317bb6e5)
6) Crate SNS -- it is messaging service in AWS
7) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/4cd87639-bf01-4063-aa12-d465a874dc30)
8) Click and on next
9) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/0dc0e260-0968-4ae0-a01a-23fd1dda1823)
10) next --> configured --> create alarm
11) Now cofirm the subscription of SNS topic -- check inbox and spame as well
12) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/a8db9c0c-26f5-406d-a2fa-7d28cd03de98)
13) Check the SNS topic as your CPU will be reached 50 %
14) Alarm is ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/db60588c-8d20-4a72-928d-86f367d2b5cb)
15) You will receive email ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/5fc3d834-91b2-4921-b0ef-85d22de05d35)
16) We can create dashboard --> name of the dashboard --> select metrics
17) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/ee6bed23-3a8b-4d1a-9ca9-d43200c31f96)




