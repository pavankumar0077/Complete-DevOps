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

Solution:
--
1) Create EC2 instance
2) Create Snapshot --> EC2 dashboard --> Snapshot
3) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/629529a6-a4a2-47ad-b81c-2152d3f70b21)
4) create it
5) After using Instance and he deleted the instances (volume will be deleted automatically with the instance)
6) He forgot to delete the SNAPSHOTs
7) We use Lambda functions it helps to delete the snaphosts

Implementation
--
1) Create lambda function
2) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/9077eea5-9df8-43b8-b8f7-36aec6a0d160)
3) Create the code REF LINKE : ``` https://github.com/pavankumar0077/aws-devops-zero-to-hero/blob/main/day-18/ebs_stale_snapshosts.py ```
4) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/5282a65f-96ea-466a-a6b0-464691e72ae1)
5) Save, deploy and create event and test it
6) Configureation --> General configurations --> Default execution timeout will 3 sec we have change to 10 sec
7) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/966415d9-f0e7-4e3c-aea5-f4d9aeffb6d4)
8) Config --> Permissions --> Role --> Click on new tab --> New policy for snaphots --> If snaphsots are finding the create policy
9) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/2c856357-1f71-41d9-a359-d4e178b9e1e8)
10) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/cc8a37ee-b2dc-4957-8926-a9eb53fb63df)
11) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/f27e49d2-c7f9-46c6-af48-83fd3d81bfb3)
12) Now we have to attach this policy to the role, Now again go to role and attach policy
13) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/aa1a7f75-04d5-430c-882e-05e99fcdd941)
14) NOW IT SHOULD NOT DELETE THE SNAPSHOT BECUASE SANPSHOT IS ATTACHED TO VOLUME AND IT IS ATTACHED TO EC2 INSTANCE.
15) Now attach EC2 policies for DescribeVolumes and DescribeInstances or Create policy as we need before and add the policy
16) Script executed without error : ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/ad20413e-9031-4ee6-a44e-b1db8f8cd861)
17) NOW DELETE THE INSTANCE (IT WILL DELETE VOLUME BY ITSELF)
18) Snapshot is still available ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/468c6b62-4951-4926-b889-ad8196d5cee9)
19) NOW IF WE RUN THE LAMBDA SCRIPT IT WILL DELETE THE SNAPSHOT AS WELL
20) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/37a86410-5709-471a-9482-c03208707f65)
21) This example is for any no of snapshorts will be deleted with this script.
22) This script basically do : If there is snapshot and it belong to volume but not attached to any instance then DELETE IT

CODE 
--
``` https://github.com/pavankumar0077/aws-devops-zero-to-hero/blob/main/day-18/ebs_stale_snapshosts.py ```
```
import boto3

def lambda_handler(event, context):
    ec2 = boto3.client('ec2')

    # Get all EBS snapshots
    response = ec2.describe_snapshots(OwnerIds=['self'])

    # Get all active EC2 instance IDs
    instances_response = ec2.describe_instances(Filters=[{'Name': 'instance-state-name', 'Values': ['running']}])
    active_instance_ids = set()

    for reservation in instances_response['Reservations']:
        for instance in reservation['Instances']:
            active_instance_ids.add(instance['InstanceId'])

    # Iterate through each snapshot and delete if it's not attached to any volume or the volume is not attached to a running instance
    for snapshot in response['Snapshots']:
        snapshot_id = snapshot['SnapshotId']
        volume_id = snapshot.get('VolumeId')

        if not volume_id:
            # Delete the snapshot if it's not attached to any volume
            ec2.delete_snapshot(SnapshotId=snapshot_id)
            print(f"Deleted EBS snapshot {snapshot_id} as it was not attached to any volume.")
        else:
            # Check if the volume still exists
            try:
                volume_response = ec2.describe_volumes(VolumeIds=[volume_id])
                if not volume_response['Volumes'][0]['Attachments']:
                    ec2.delete_snapshot(SnapshotId=snapshot_id)
                    print(f"Deleted EBS snapshot {snapshot_id} as it was taken from a volume not attached to any running instance.")
            except ec2.exceptions.ClientError as e:
                if e.response['Error']['Code'] == 'InvalidVolume.NotFound':
                    # The volume associated with the snapshot is not found (it might have been deleted)
                    ec2.delete_snapshot(SnapshotId=snapshot_id)
                    print(f"Deleted EBS snapshot {snapshot_id} as its associated volume was not found.")
```

Do with CloudWatch
--
1) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/33d789fd-36b1-45f4-995d-98269f0aa515)
2) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/b6edeb57-fa1c-4a72-b898-d575c26db1c4)


BOTO 3 DOCS : ``` https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/ec2.html ```

