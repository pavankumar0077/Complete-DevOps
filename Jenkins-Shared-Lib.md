# JENKINS SHARED LIBRARY 

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/74b20602-190d-4e9f-a6a4-b86d78b4d9af)

REF LINK : https://github.com/pavankumar0077/Jenkins-Zero-To-Hero/tree/main/shared-libraries
REF LINK : https://github.com/praveen1994dec/jenkins_shared_lib/tree/main/vars

Problem
--
1) We hava 500 + microservices then that are individually developed and deployed, For every microservices we have to write jenkins file for the CI CD part.
2) Now we you want to change the configuration like some commands in pipeline, Then going to 500 microservices and changing the config is very big task, Even you have wrote
jenkins file for once and rest copies for 500 microservices.

Solution -- Jenkins Shared Library
--
1) It is nothing but a commone or repetative or reusable code Ex: MAVEN BUILD IS same for all the applicaitons.
2) Jenkins provide a concept called SHARED LIBRARIES, You need to put the reusable code as a library in GIT REPO anf reference the LIBRARY in our stage or pipeline or jenkins file
3) Now we will take reference the library and use it in the jenkins file
4) ``` @Library("my-shared-library) _ ``` This should to used to import the library and add the library details in the System or Tool settings in jenkins
5) Change in one place and it will changed in all the jenkins files.
6) - vars
   - src
   - resources
  
Normal pipeline
--
```
pipeline {
    agent any

    stages {
        stage('Greetings') {
            steps {
                echo 'Hi Techie'
            }
        }
    }
}

```
Step 1: Create a Git Repo for JENKINS SHARED LIBRARY
Step2 : Create vars folder --> Create the scripty --> maven-test.groovy (Ex) (Camalcase - helloWorld.groovy)
Step3: Congigure the shared library in jenkins --> Managme jenkins --> systems --> Global piple 
![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/3b371921-2e5d-4ea6-b127-e16f9525a1bd)
Step4: In the jenkins file we have to import the shraed lib --> ``` @Library("my-shared-library") _ ``` Here _ is represents everything in the library

Shared lib pipeline
--
```
@Library("my-shared-library") _
pipeline {
    agent any

    stages {
        stage('Greetings') {
            steps {
                helloWorld()
            }
        }
    }
}
```
Here we are using helloWorld() --> IT IS FILE NAME NOT THE FUNCTION NAME 
![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/b9786411-d25c-4b38-99c8-cff7864070e2)

```
```
def call() {
  sh 'mvn clean install'
}

```
```
@Library("my-shared-library") _
pipeline {
    agent any

    stages {
        stage('Greetings') {
            steps {
                helloWorld()
            }
        }
        
        stage('Maven Build') {
            steps {
               mvnBuild()
            }
        }
    }
}
```



