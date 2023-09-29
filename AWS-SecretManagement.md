# AWS SECRETS MANAGEMENT 

### AWS SECRETS MANAGEMENT SERVICE
- SYSTEMS MANAGER - aws provided
- SECRECTS MANAGER - aws
- HASHICORP VAULT - not aws provided (Most of the organizations use)

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/4d567fa8-50c6-4435-bea4-6e04c32aaa25)

Systems Manager - Ex: in CICD process we have used docker credentials, These credentials are stored in the SYSTEM MANAGER --> Exactly in the parameter store (docker username, password and url has stored as STRING)
- It is very easy to retrive --> like any other AWS service if it tries to access this information like secrets --> we just need to grant that IAM ROLE
- Simple to use and simple to integrate as well.
- Infomation that should be stored is sensitive but not that much -- WE CAN USE SYSTEMS MANAGER

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/67f14270-bc8c-4972-848a-a2cab5d2ca11)

Secret Manager - Let's say we are using CERTIFICATES, certificates should be expired in certain duration like once in 180 days, our certificates will be expired, IT SHOULD BE AUTOMATICALLY ROTATED (RENEWED) 
- Even your certificate is exposed or certificate is compremised like someone has the certificates public key.
- If we are rotating certificates for 180 days or 360 days like that so there is very less chance of compremise
- SYSTEM MANAGER CAN DO THIS AUTOMATICALLY
- Even passwords - Let's say we have passwords which are DB related. We are using this in DAY TO DAY activies. So DB passwords are highly confidention so ROTATE THESE PASSWORD for every 90 days.
- Information will be secured.
- DB's, API Token's,
- HIGHLY CONFIDENTIAL INFORMATION SHOULD BE STORED IN SECRET MANAGER
- PASSWORD ROTATION POLICY.
- CARGES HIGH AS COMPARED TO SYSTEMS MANAGER
- EVEN WE CAN CREATE OUR OWN CUSTOM ROTATION POLICY AS WELL

### NOTE : ALWAYS GO WITH SYSTEMS MANAGER + SECRETS MANAGER (Combined)

### THE ABOVE 2 SOLUTIONS ARE ONLY FOR AWS CLOUD

HASHICORP VAULT (OPEN SOURCE)
--
![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/017943da-03ba-482f-9a3e-9de4abfc3810)
- WE CAN USE WITH ANY CLOUD PROVIDERS LIKE AWS, GCP, AZURE
- Hashicorp vault is dedicated secrets management solution.
- IT IS OPEN SOURCE, Community driven project
- Many featuers and encryptions are used in Hashicorp 
 
