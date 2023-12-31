# CI CD 
1) Full stack application made with Node JS having Front-end, Back-end and DB
2) SonarQube
3) OWASP Dependency check tool (Vulnerbaility Scan on source code)
4) Trivy (Uses multiple DB for matching the issues in dependencies to find the issues  - file scanning)
5) Docker-compose to deploy the applications
6) Multi-container application, front-end backend and db

Plugins used
--
1) Docker	 
2) OWASP Dependency-Check	 
3) NodeJS
4) SonarQube

Configure plugins -- Tools
--
Dashboard --> Manage Jenkins --> Tools 
![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/dd3de889-69c7-40ab-ac3b-8955c12ffbc8)
![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/9e49ef4e-e90f-4e75-a350-ae6490020f8c)
![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/25dedf6f-063f-45a1-b06f-2e37e60a2e61)
![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/e2c07c93-c73e-42db-94e6-255b368f1b4a)

Create pipeline
--
1) Name --> Select pipeline
2) General --> Discard old build ( We are only keeping last 2 builds )
3) Keeping the history of 2 builds (BEST PRATICE)
4) Pipeline section -- Pipeline script
5) Stage 1 -- Git check -- It create a local copy of the repo in the jenkins path in local machine
6) Stage 2 -- Perform vulnerability scanner using OWASP dependency check
7) For OWASP arguments ``` https://jeremylong.github.io/DependencyCheck/dependency-check-cli/arguments.html ``` and location is from root (./) and pattern is xml format.
8) Stage 3 - Trivy tool should installed in local (VM) Link ``` https://aquasecurity.github.io/trivy/v0.18.3/installation/ ```
9) FS means - File System scan
10) Stage 4 - SonarQube it should be running in local, Run docker image ``` sudo docker run -d --name sonarqube -p 9000:9000 -p 9092:9092 sonarqube ``` user and pws is ```admin ````
11) Open 9000 web ---> admin username and pwd --> Go to adminstration --> Security --> User --> Create Token --> For connection sonar to jenins it is credentail
12) Add credentails to jenkins ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/d7b2f1be-9b10-442b-a7a1-961a41f70519)
13) Add sonar server url -- ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/c118b11e-21e3-4b66-990f-cfb892bfa068)
14) For sonar we need to add env variable with sonar tool home path
15) Here we need to check for pipeline syntax ``` sonarwithcredentails ``` and give only sonar (server-name) which we configured in tools
16) sh (''') used to multiple line commands and ("") for single command
17) project name and projectkey we can anything related to project
18) Add this plugin to install atleast version of java **Eclipse Temurin installer** and add java11 and java17 to not to get any errors.
19) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/c8613634-79ea-44ff-abdf-aefe15395022)
20) ``` tools{
        jdk 'jdk11'
    }
    ```
21) If we are using different then we have to mention like this in the pipeline in the up, TOOL TYPE AND TOOL NAME like jdk11 or jdk17 which we have mentioned in the jdk installation in tools
22) Stage 5: Install node -- dependencies --> We have to mention in the tools section in the starting of jenkins file as we did for java
23) Stage 6 and 7 adding backedn and frontend --> In jenkins we con't move like this using CD so we use in this format
24) ``` stage('frontend') {
            steps {
                dir('/root/.jenkins/workspace/Bank/app/frontend') {
                    sh "npm install"
                }
                
            }
    ```
25) In the above step dir should to jenkins dir in our case the location will be ``` /var/lib/jenkins/workspace/Bank/app/backend ```
26) 















```
pipeline {
    agent any
    
    tools{
        jdk 'jdk17'
        nodejs 'node16'
        
    }
    
    environment{
       SCANNER_HOME = tool 'sonar-scanner'
       JENKINS_HOME = '/var/lib/jenkins' // Set the HOME directory
    }
    
    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/jaiswaladi246/fullstack-bank.git'
            }
        }
        
        stage('OWASP FS SCAN') {
            steps {
                dependencyCheck additionalArguments: '--scan ./app/backend --disableYarnAudit --disableNodeAudit', odcInstallation: 'DC'
                    dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        
        stage('TRIVY FS SCAN') {
            steps {
                sh "trivy fs ."
            }
        }
        
        stage('SONARQUBE ANALYSIS') {
            steps {
                withSonarQubeEnv('sonar') {
                    sh " $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Bank -Dsonar.projectKey=Bank "
                }
            }
        }
        
        
         stage('Install Dependencies') {
            steps {
                sh "npm install"
            }
        }
        
        stage('Backend') {
            steps {
                dir('var/lib/jenkins/workspace/Bank/app/backend') {
                    sh "npm install"
                }
            }
        }
        
        stage('frontend') {
            steps {
                dir('var/lib/jenkins/workspace/Bank/app/frontend') {
                    sh "npm install"
                }
            }
        }
        
        stage('Deploy to Conatiner') {
            steps {
                sh "npm run compose:up -d"
                args '-v $HOME:/home/jenkins'
            }
        }
    }
}
```
