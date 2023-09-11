# AWS IAM

1) A general example of bank with authenticated and authorization to access
![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/674e8189-5126-4f8c-ab65-44a33870881f)


![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/6506b912-b991-4381-8868-7d8f650b3d2f)

1) Let say for example we have AWS Accounts which has some service are used. While creating aws account we have root access
2) If Devops engineer gives root access to everyone in the organization they will directly come to this account and access the service
3) Sometimes one user knowingly and unknowingly deleted the information present in the DB service because it had a root access
4) Complete application is messed up and if the information can be come back then application will not work.

# IAM

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/73051769-3ebf-4821-b88f-b09e060c6c3d)

1) IAM  will do authentication and authorization for the users, ABOVE PROBLEM SOLVED BY IAM
2) Let say for example a new employee as joined the company and we need AWS access, So he request for the access to DEVOPS ENGINEER
3) Now the DEVOPS engineer will ask for the employee what service we want the access.
4) Now DEVOPS engineer creates the account and give permission by using POLICIES to specific service that too read access.

# IAM Components
1) USERS
2) POLICES
3) GROUPS
4) ROLES

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/23c4a4d5-0e35-449e-8226-0fe5bb726ebd)

###  USERS
1) You have created a USER, for an employee that is called (AUTHENTICATION)

### POLICIES
1) User is created, What the USER has to be done is take care by POLICIES. (AUTHORIZATION)
2) After creating the user, he has to do or work with some services then policies will be added for that user.
3) We need to create the user and attach some polices to the user.

### GROUPS
1) Groups is needed as a DEVOPS engineer, In an organization their are employee onboarding and leaving the organization frenquently so creating the user and deleting the user will consume more time.
2) To solve this problem we create GROUPS as a DEVOPS engieer to improve the efficency we will automate the proccess by saying that how i categorize by
DEV's, QA, DB admins, Others.
3) First we will create these groups DEV, QA, DB ADMINS AND OTHERS
4) If someone joined in the organization with emp id 101, then they will send a JIRA Request with and Category like Dev, Qa and etc, Based on that we will create the user and we will keep that user in that GROUP.

### ROLES
![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/e07988d2-d35a-4394-a967-f9cca3ea54ee)

Ex: I am Dev, and there is DB service that i am using which is there in AWS. My application is in private cloud when customer want to acccess the application,As a dev I need to get the information from the DB service and give to the customer. In this case we can create USER for this application. It is like application need access to the DB.In this case when ever you are using the application use this application with the ROLE.
1) ROLE have username and password which is temparary
2) We can not hack with thses username and passwords
3) ROLES are similar to USER but not 100% as user,created only for TEMPARARY Purpose
4) ARE created only to talk with the AWS services 

# CREATE USER
1) Root login --> IAM --> Users --> name and console accces  --> I want create a IAM user --> Auto - gen password(So that user can user own password when logged in)
2) Created an account without AUTHORIZATION- no use
![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/274c4598-c7a2-44b4-8540-a9bee21765df)
3) Adding polices --> IAM --> USERS --> ADD polices --> S3 (Read only, Full acess, Write only) select anyone option
4) Some of the service bydefault will will there, called AWS managed policies --> CUSTOM POLICY -- Like if we want to give permission to S3 with only view to specific buckets then go with custom policies.



   




