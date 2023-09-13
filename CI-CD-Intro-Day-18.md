# CI CD 

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/58f6060d-99a8-423d-a266-c86c0ee64923)

BASIC STEPS IN CICD PIPELINE TO BE CHECKED
--
1) unit testing ✓
2) Static Code Analysis ✓
3) code Quality / vulnerability
4) Automation ✓
5) Reports
6) Deployment ✓

1) UNIT TESING : Example we have caluculator application and we are writing addition functionality. Addition like
a+b = c, and we are passing 2 + 3 = 5. Every application developer has to do this checking own code is called unit testing
This process can not be manual everytime when new code is writen 100's of times they can not run manually do delivering to the customer or form one stage to another stage

2) Static Code Analysis : In the calculator application we have declared 20 variables and used only 3 variables, The problem
is lot of memory allocated is wasted here, To avoid this we use Static Code Analysis to verify like syntaxtical right, well formatted , imdentention.

3) Code Quality or Vulnerability : Ex: We got a new version of android, and upgrade to the new version and to you came to know that
their is security vulnerability their can a hacker who can then that is very bad user experience So before we move to next stage we will
check the code quality.

4) Automation : Automation testing, Functional we can writen does not impact other functions like sub, mul and div.
Verifying the application in END TO END manner.

5) Reports : Very essential to every Organization. Code quality, unit testing coverage

6) Deployment : Deploying your application in a platoform where the end user can access this application.

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/fd6f9e49-a764-4741-b4aa-4f8d8db743bb)

NOTE: TO do all this stages or steps manually it takes a lot of time. So once the application code commit or pushed to
Github then CI CD pipeline will start automatically.

STEP: 1
![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/e4001316-6469-4c81-8ce8-4304b95d67b0)
1) In Jenkin DEVOPS engineers has to will configure configure the installation that are required to run the
Piepline and Jenkins will automatically run all the stage when new code is pused to REPO.

NOTE: With this we will improve the effeiency of the application.

STEP: 2
![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/5a43b0d2-d041-41eb-a7a3-bcc276e11b2b)
1) This Jenkins prompt our application to Dev, Stage and Prod
2) Org's divide the env's as DEV,STAGE AND PROD - End user
3) Usually Org's Divides their working ENV's to multiple like DEV, STAGE OR UAT, PROD 

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/7cd356fe-f409-494d-8a89-c3a35253cbd1)

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/dcd8d979-349b-4d25-a9ab-ab27b8e89abe)

**GITHUB Actions** :  **Github Actions are another way of doing CI CD just like jenkins, Whenever a code change is made it is spin up a kubernetes pod and it will create a docker container for us and everything will get executed in the docker container if you are not using it the server or worker which is used to run this docker containers will be used by some other projects like a shared resources.**

### NOTE :  Most of the open source projects us GITHUB ACTIONS

```
Jenkins is event driven whenever we get a pull request we can do using webhooks 
GITHUB actions are by default evernt driven and continouse intergration and continous delivery solution, we can also interate the pipelines from one project to another project their are many advantages that github actions will provide.
```


