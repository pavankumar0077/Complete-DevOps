# AWS Cloud Formation Templates (IAC) 

IAC
--
![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/217e5327-bb75-4e98-a409-2b847dc7a07c)
![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/33b8187e-b3ff-4d08-839a-c80363cc84b6)

Principals of of IAC
--
1) We write code to create infrastructure
2) **Declarative in Nature** -- What you see is what you have --> Template --> By reading these are the resources availabe in AWS --> Code review 
3) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/06808bc0-3766-4218-a859-2dab080c59f9)
 -- We can take this templates and store them in Git or S3 or any other -- Trace back the versions
4) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/1a0440bf-c8e0-4371-a8d0-6f0654daf7de)

When we use CFT, When to use CLI
--
![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/2503f91d-b89b-4afa-9936-1fe489b53739)

1) CLI -- As a Devops engineer use CLI when you want to execute short or quick actions (Like listing s3 buckets, ec2 instance listing them)
2) When you want to use simple script to get resouces which are using in AWS then use CLI

### CFT 
1) CFT is for creating actual resources like 10 ec2 instances, s3 and etc

Featues of CFT
--
![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/6475b477-eff6-4a95-b278-965da0df4749)

1) It supports both JSON AND YAML
2) **Crating Infra (Primary)**
3) **Drift Detection** : Ex: We have created EC2 + S3 using CFT, Like someone went to the UI and they modified the changed something like s3 versioned enabled or disabled something like that
4) In CFT we have option called Detect Drift --> Periodically check and changes 

**NOTE** :  **For example we have a YAML in local machine, Now how do you submit to AWS CFT (created using CLI OR WEB UI) , IN CFT we have STACKS --> Create Stacks --> import YAML which is in local** 

CFT TEMPLATE STRUCTURE
--
1) Resources are imp (Mainly)
2) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/c5882a85-771c-4eea-a4da-8b89090ea631)

**CFT DOCS** : ``` https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/Welcome.html ```, ``` https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/template-guide.html ```, ``` https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/template-anatomy.html ```

```

AWSTemplateFormatVersion: "2010-09-09" -- VERSION
Description: A sample template  -- DESCRIPTION OF THE TEMPLATE SPECIFIC 
Resources:  -- WE CAN CREATED ANY NO.OF RESROUCES 
  MyEC2Instance: #An inline comment   ---NAME CAN BE ANYTHING
    Type: "AWS::EC2::Instance"  -- LIKE S3, EC2 AND ETC
    Properties: 
      ImageId: "ami-0ff8a91507f77f867" #Another comment -- This is a Linux AMI -- we parameterize the imageid if the same template is used by different teams
      InstanceType: t2.micro 
      KeyName: testkey
      BlockDeviceMappings:
        -
          DeviceName: /dev/sdm
          Ebs:
            VolumeType: io1
            Iops: 200
            DeleteOnTermination: false
            VolumeSize: 20
```

