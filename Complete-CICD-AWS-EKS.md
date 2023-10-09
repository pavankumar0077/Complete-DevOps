#  

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/d2048c0d-96d4-419b-9540-2fa1757d4da0)

- While creating the instance in aws at the time of creation of instance we can give script to install -- User data

1) Here we are using pipeline with parametes like if you are want to create or delete the pipeline then we can chose it
```
 parameters {
        choice(name: 'action', choices: 'create\ndelete', description: 'Choose create/Destroy')
    }
```

2) If we want to run the stage with condition like if the choice is create then only we have run the stages, if not, no need to run the stage if we want to delete it, We use When expression to achieve this 
```
```
when{expression {params.action == 'create'}}
```
 stage('Maven Integration Test'){
 when{expression {params.action == 'create'}}
            steps{
                script{
                    mvnIntegrationTest()
                }
            }
        }
```
3) SonarQube and Jenkins works on 2-WAY HANDSTACK
  - While checking Quality gate status sonar qube will sends the status to jenkins
  - So we need to configure sonar webhook
  - Sonarqube will sends the reponse about the quality gate status for that we need WEBHOOK

4) Sonar dashboard --> Adminstration --> webhook
![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/92b97b4c-18d6-4666-a154-3c21974a22ba)


5) We can import multiple libraries in jenkins file
```
@Library("my-shared-library", "abc-lib", "xyz-lib) _
```

6) We have to add the docker details, So for that we need to add String values and we can also give default values as well
```
 parameters {
        choice(name: 'action', choices: 'create\ndelete', description: 'Choose create/Destroy')
        string(name: 'ImageName', description: "name of the docker build", defaultvalue: "javaapp")
        string(name: 'ImageTag', description: "name of the docker build", defaultvalue: "v1")
        string(name: 'AppName', description: "name of the application", defaultvalue: "springboot")
    }
```
7) 
