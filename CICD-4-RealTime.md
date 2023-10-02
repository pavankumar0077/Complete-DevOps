# Python Flask Project that monitor the CPU resources of the instance real time

- Makefile -- It contains specific set of actions which we can chose to perform


Plugins Needed
--
- SonarQube Scanner
- OWASP dependency-check
- docker
- docker pipeline
- docker-build-step
- Eclipse Temurin Installer --- For jdk 17



For SonarQube -- we need to configure TOOLS, AS WELL AS SERVER,
NOTER l IF YOU GET ANY ERROR FOR SONAR LIKE USER AND PASSWORD THEN IF HAVE MISSED   SONAR-WITH CREDENTIALS BLOCK 

CODE REF : https://github.com/jaiswaladi246/Python-Webapp/tree/main

Stage - Docker
- We can nnot script directly we have to enclose in the script tag
- check for withDockerRegister pipelines syntax

Jenkins file
--
```
pipeline {
    agent any
    
    tools{
        jdk 'jdk17'
    }
    
    environment {
        SCANNER_HOME = tool 'sonar-scanner'
    }

    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/jaiswaladi246/Python-Webapp.git'
            }
        }
        
        stage('OWASP') {
            steps {
                dependencyCheck additionalArguments: '--scan ./ ', odcInstallation: 'DC'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        
        stage('Trivy Fs Scan') {
            steps {
                sh "trivy fs ."
            }
        }
        
        stage('SonarQube') {
            steps {
                withSonarQubeEnv('sonar') {
                sh '''$SCANNER_HOME/bin/sonar-scanner -Dsnoar.projectName=Python-Webapp \
                -Dsonar.java.binaries=. \
                -Dsonar.projectKey=Python-Webapp '''
                }
            }
        }
        
         stage('Docker Build & Image') {
            steps {
                script {
                    // This step should not normally be used in your script. Consult the inline help for details.
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        sh 'make image'
                    }
                }
               
            }
        }
        
        stage('Trivy Docker image scan') {
            steps {
                sh "trivy image adijaiswal/python-webapp:latest"
            }
        }
        
        // stage('Docker Push Image') {
        //     steps {
        //         script {
        //             // This step should not normally be used in your script. Consult the inline help for details.
        //             withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
        //                 sh 'make push'
        //             }
        //         }
               
        //     }
        // }
        
        stage('Build to Docker Container') {
            steps {
                script {
                    // This step should not normally be used in your script. Consult the inline help for details.
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        sh 'docker run -d -p 5000:5000 adijaiswal/python-webapp:latest'
                    }
                }
               
            }
        }
    }
}
```

