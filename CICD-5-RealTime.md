# BUILD AND DEPLOY .NET BASED APPLICATION 

We have MAKE file with all commands

Plugsns required
--
- sonarQube
- owasp dp check
- docker
- docker plugin
- docker build step

```docker run -d --name sonar -p 9000:9000 sonarqube:lts-community``` TO RUN THE SONAR AS DOCKER CONTAINER

REF LINK : https://github.com/jaiswaladi246/DotNet-DEMO

in THE DOCKER FILE WE HAVE IMAGE_BASE = 6.0-alpine
- ALPHINE -- VERY SMALL SIZE IMAGE

OWASAP
- IT will scan the dependencies and library that are used in the project
- It will find out any specfic version is used is vulnerble or not

TRIVY
- It performs vulnerabaility scanning on our docker image as well as file system
- OWASAP has the limited access to the Databases OF THE VULNERABILITIES. WHILE TRIVY HAS VERY VAST OF DATABASE WHCIH IT USES FOR SCANNING THE DEPENDENCIES
- wE HAVE TO INSTALL IN WHERE JENKINS IS RUNNING NOW

Install MAKE in jenkins running instance as application have make file

Docker permission error
-``` sudo chmod 666 /var/run/docker.sock``` Use this command on the docker user 

Change the docker name image
- Chnage the docker username and image in MAKE FILE

JENKINSFIL
--
```
pipeline {
    agent any
    
    tools{
        jdk 'jdk17'
    }
    
    environment{
         SCANNER_HOME= tool 'sonar-scanner'
    }

    stages {
        stage('Git-checkout') {
            steps {
                git 'https://github.com/jaiswaladi246/DotNet-DEMO.git'
            }
        }
        
        stage('OWASP DP Check') {
            steps {
                dependencyCheck additionalArguments: ' --scan ./ ', odcInstallation: 'DC'
                    dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        
        stage('Trivy FS SCan') {
            steps {
                sh "trivy fs ."
            }
        }
        
        stage('Sonar Analysis') {
            steps { 
                withSonarQubeEnv('sonar') {
                  sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=dotnet-demo \
                    -Dsonar.projectKey=dotnet-demo '''
              }
           }
        }
        
         stage('Docker Build & Tag') {
            steps { 
               script{
                   // This step should not normally be used in your script. Consult the inline help for details.
                    withDockerRegistry(credentialsId: 'docker-cred') {
                        sh "make image"
                    }
               }
           }
        }
        
        stage('Docker push') {
            steps { 
               script{
                   // This step should not normally be used in your script. Consult the inline help for details.
                    withDockerRegistry(credentialsId: 'docker-cred') {
                        sh "make push"
                    }
               }
           }
        }
        
        stage('Deploy to container') {
            steps { 
                sh "docker run -d -p 5001:5000 adijaiswal/dotnet-demoapp"
           }
        }
    }
}
```
