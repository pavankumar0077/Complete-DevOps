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

