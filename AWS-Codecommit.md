# AWS CodeCommit

1) AWS provides a comprehensive set of CI/CD (Continuous Integration/Continuous
Deployment) services that enable developers to automate and streamline their
software delivery processes.
2) AWS CodePipeline, AWS CodeBuild, and AWS CodeDeploy are the key services
involved in achieving CI/CD on AWS platform.
3) AWS Managed Service
4) It stores the code in PRIVATE GIT REPO's

CI-CD IN AWS
--
6) AWS CodeCommit
7) AWS CodePipeline
8) AWS CodeBuild
9) AWS CodeDeploy

## Advantages
- Managed Git
- Scalability
- Reliability

Create CodeCommit 
--
**NOTE: DON'T USE ROOT ACCOUNT, AWS DOESN'T RECOMMED AND IF USED SOME FEATURES WILL NOT WORK LIKE SSH and etc.**
1) AWS console --> codecommit
2) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/d9cbd99a-9a7f-4bff-af40-28e59304c898)
3) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/f65b71f9-ec6b-45a3-a5ae-2350e4688669)
4) We have same features as GITHUB, But not all the features are available
5) But using WEB UI you can only upload the files one by one
6) Edit --> update the files from WEB UI
7) Crate new user or attch policies to exiting uers
8) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/a7d9846f-645f-4c68-9251-1512062becf2)


Setup git credentials
--
1) https://us-east-1.console.aws.amazon.com/codesuite/codecommit/repositories/demo-repo-cc/setup?region=us-east-1
2) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/a80efa26-3f53-4468-8a7e-7b60efff14a2)
3) You can able to clone it. USE GIT only sometimes other terminals will not prompt for credentials

Add files and commit as as normal git
--
```
 nano testing.txt
  258  git status
  259  git add .
  260  git status
  261  git commit -am "testing" 
  262  git push -u origin
  264  git pull
```

Disadvantages of CodeCommit
--
- Features
- AWS Restricted
- Less integrations with services outside AWS


