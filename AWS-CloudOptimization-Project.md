# AWS Cloud Optimization Project

### Ex: Lets say we have dev and granted access to create EC2 instance, So he created EC2 instance and he attached volume. The volume is filled with the very sensitive information
- Let say you want to take backup of the volume -- He have taken backup each and everyday nothing but SNAPSHOTS -- After a while the instance is no more used so we deleted the instance and volumes as well
- For example he was using some external volume for ec2, He deleted the EC2 instance and he forgot to delete the volume. The snapshots we took is also forgotten to delete -- Now aws will be charging
- for all the snapshots. Like this we have other example as well.


- S3 buckets are created by dev and he is not using it, the content in the S3 bucket -- charge will be increased

- In EKS cluster we have 200 resources which are not using now, Charges will charged

- Stale resources --> 1) Notification with SNS or 2) Delete the resources

- These should make sure cloud cost should go down.

### Problem state (EBS volume snapshots) -- multiple snapshot later the dev deleted the instance and forgot to delete volumes 
1) Lambda function --> written in python --> It will talk to AWS using API's
2) Step1: To fetch all the EBS snapshots
3) Step2: To filter out the snapshots that are STALE.
4) Step3: Identify and delete the sanpshots
