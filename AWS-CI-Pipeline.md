# AWS CodePipeline - CI

REF link : https://github.com/pavankumar0077/aws-devops-zero-to-hero/tree/main/day-14

IAM ROLE -- FOR SERVICE NOT FOR IAM USER
BUILDSPEC -- IS LIKE JENKINS PIPELINE (STEPS AND STAGES)

Create code pipeline 
--
1) Search for CodeBuild
2) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/7eeab974-49a1-48a4-b28b-e2c954cdfd73)
3) connect to github using oauth or token
4) Set up env for pipeline
5) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/272a03ea-0f73-4379-9ed8-0396d69a5f28)
6) Build spec -- like jenkins pipeline
7) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/46bb2146-b888-446d-b774-ce7c8f31cabb)
```
version: 0.2

env:
  parameter-store:
      DOCKER_REGISTRY_USERNAME: /myapp/docker-credentials/username
      DOCKER_REGISTRY_PASSWORD: /myapp/docker-credentials/password
      DOCKER_REGISTRY_URL: /myapp/docker-credentials/docker-url
 
phases:
  install:
    runtime-versions:
      python: 3.11
  pre_build:
    commands:
      - pip install -r day-14/sample-python-app/requirements.txt 
  build:
    commands:
      - cd day-14/sample-python-app
      - echo "Building Docker Image"
      - docker build -t "DOCKER_REGISTRY_URL/DOCKER_REGISTRY_USERNAME/simple-python-flask-app:latest"
      - docker push "DOCKER_REGISTRY_URL/DOCKER_REGISTRY_USERNAME/simple-python-flask-app:latest"
  post_build:
    commands:
      - echo "Build is Successful"
      # - command
#reports:
  #report-name-or-arn:
    #files:
      # - location
      # - location
    #base-directory: location
    #discard-paths: yes
    #file-format: JunitXml | CucumberJson
#artifacts:
  #files:
    # - location
    # - location
  #name: $(date +%Y-%m-%d)
  #discard-paths: yes
  #base-directory: location
#cache:
  #paths:
    # - paths
```
9) we can use new role or existing role
9) These are the build commands that are according to our application requirements
10) All the others are bydefault and click on create

Stroing the credentials in secured way
--
1) Search for Systems Manager
2) Select perameter store
3) And create credentails for docker username, password, and docker-registry url
4) Ex:
5) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/8eace2e5-3b6b-4112-b1d7-37f12748e56d)
6) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/a0d7822d-328f-490e-a692-d231ba7c9498)
7) In the same create for username and password as well
8) Click on create
