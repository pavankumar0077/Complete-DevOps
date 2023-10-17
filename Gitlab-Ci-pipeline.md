# CI PIPELINE ON GITLAB

Complete Pipeline
--
![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/e7ccb0c9-304d-4a33-ac37-45b5e5a715a9)


Stages
--
![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/e04c4bb4-6e29-426c-8865-8ccb5895d673)


![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/9cda9f4a-69d4-4c3d-9501-d3f43368a361)
1) Project Runners : Project runners are like self hosted runners we have control of where it should run and other details of pipeline
2) Shared Runners : In shared runners the pipeline will be exected someone and we don't have much information about it.
3) JENKINS, we create Jenkinfile in the root of the github repo for jenkins, in GITHUB we use .workflow folder and we use workflows as required by the CI
4) In GITLAB it will like instead of jenkinsfile we create **".gitlab-ci.yml"** file
5) Stages :
6) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/faabf4dc-0149-4dd9-ab5d-4a135837b079)
7) For Securing credentials we are using GITLAB variable
8) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/c088fb30-4efd-44ee-bace-8c6bb0761c62)
9) Install GITLAB RUNNERS in Ubuntu ``` https://docs.gitlab.com/runner/install/linux-repository.html ```
10) 
