![image](https://github.com/user-attachments/assets/819bc54a-2b11-4e95-95b5-5ffd3a5ffd01)# Going Bastion-less: Accessing Private EC2 instance with Session Manager

Session Manager
--
- can be used to access instances within private subnets that allow no
ingress from the internet. (You don't require to have an inboud connection from the internet to you env)
- AWS SSM provides the ability to establish a shell on your systems
through its native service, or by using it as a tunnel for other protocols,
such as Secure Shell (SSH)

Benefits:-
--
- It will log the commands issued during the session, as well as the
results. You can save the logs in s3 if you wish.
- Shell access is completely contained within Identity and Access
Management (IAM) policies, you won't need to manage SSH keys
- The user does not need to use a bastion host and Public IPs.
- No need to open the ports in the security groups

DrawBacks:
--
- You will need to allow SSH inbound rule at your
bastion
- You need to open ports on your private EC2
instance in order to connect it to your bastion
- You will need to manage the SSH key credentials of
your users:
- You will need to generate an ssh key pair for each
user or get a copy of the same SSH key for your users
- Cost: The bastion host also has a cost associated
with it as it is a running EC2 instance.

Access without Bastion Host
--
![image](https://github.com/user-attachments/assets/93e2d64c-0862-47f7-b3a3-0bd5d57f3d1d)

Create a Role for SSM
--
- Named as SSM-Port-Forwarding
- AmazonSSMManagedInstanceCore
- ![image](https://github.com/user-attachments/assets/0feb039e-b04b-40f3-a370-a7c637366ff3)
- ![image](https://github.com/user-attachments/assets/bbff3fe5-b5d0-4911-b91e-70a6e9f39d95)
- Here we are allowing ports of internet network only. - In public network.
- Install session manager - ``` https://docs.aws.amazon.com/systems-manager/latest/userguide/install-plugin-debian-and-ubuntu.html ```
- aws configure - aws cli
-
```
Session Manager Command: -

aws --profile AWS-LGTICW ssm, start-session --target <instance-id> --document-name AWS-StartPortForwardingSession --
parameters '{"portNumber": ["3389"], "localPortNumber": ["70"]}' --region us-east-1

ssh -o StrictHostKeyChecking=no -i lgticw.pem ec2-user@localhost -p 90
```
Start the sessions manager
--
![image](https://github.com/user-attachments/assets/141d65a9-0268-482b-964a-595fa6016a16)

- Use the above commands

How to connect with linux intance with private ip address
--
- ![image](https://github.com/user-attachments/assets/aaf242a7-9213-42ee-8d2f-2e2cdd83174d)
- ![image](https://github.com/user-attachments/assets/37993f30-f311-4388-af17-9d93db41c285)
- ![image](https://github.com/user-attachments/assets/bfe7a5bf-3a37-4713-8cdb-ff69d266a5fe)
-
```
ssh -o StrictHostKeyChecking=no -i Lgticw.pem ec2-user@localhost -p 9091
```
or 
```
ssh -o -i Lgticw.pem ec2-user@localhost -p 9091
```
- ![image](https://github.com/user-attachments/assets/6dcb8bd2-f62c-4e8c-94ad-b5312c42e014)

