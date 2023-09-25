# JAVA PROJECT WITH 3 TRIE ARCHITECTURE 
### FRONT END, BACKEND AND DB

Plugins used
--
1) Sonar
2) Docker
3) OWASP Dependency check
4) Nexus Repo management
5) config file provider -- for nexus repo
6) Plugin maven integration


# JENKINSFILE
```
pipeline {
    agent any
    
    // We have mention the tools what are using in this pipeline like name and type. We have 3 jdk version so now we are using jdk 11. we need to mention as per the code developed
    tools{
        jdk 'jdk17'
        maven 'maven3'
    }
    
    // Fir sonar qube we need to create env and name should be what we have configured in the tools 
    environment{
        SCANNER_HOME = tool 'sonar-scanner'
    }

    stages {
        stage('Git checkout') {
            steps {
               git branch: 'main', url: 'https://github.com/durgapraveena/Ekart.git'
            }
        }
        
         stage('Compile') {
            steps {
               sh "mvn compile"
            }
        }
        
        // File system scan dependency 
        stage('OWASP FS-Scan') {
            steps {
               dependencyCheck additionalArguments: '--scan ./ ', odcInstallation: 'DC'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        
        // ("") -- USED for single command (''') used for multiple commands 
        // Any tool executable will be present in bin
         stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonar'){
                   sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Shopping-Cart \
                   -Dsonar.java.binaries=. \
                   -Dsonar.projectKey=Shopping-Cart '''
               }
            }
        }
        
        stage('Build') {
            steps {
               sh "mvn package -DskipTests=true"
            }
        }
        
    }
}
```
1) tools we need to configure what tools we are using in the pipeline according to the  tools -- name that we have mentioned in the tools sections in the config
2) for sonar we need to create env and we need to mention the name of the sonar server which will be present in the configure jenkins - system
3) for nexus we need to add the dependencies in pom.xml file
```
<distributionManagement>
    <repository>
      <id>nexus</id>
      <name>Releases</name>
      <url>http://localhost:8081/repository/maven-releases</url>
    </repository>
    <snapshotRepository>
      <id>nexus</id>
      <name>Snapshot</name>
      <url>http://localhost:8081/repository/maven-snapshots</url>
    </snapshotRepository>
</distributionManagement>
```

EX: 
```
</plugin>
        </plugins>
    </build>

         <distributionManagement>
    <repository>
      <id>maven-releases</id>
      <name>maven-releases</name>
      <url>http://192.168.138.123:8081/repository/maven-releases/</url>
    </repository>
    <snapshotRepository>
      <id>maven-snapshots</id>
      <name>maven-snapshots</name>
      <url>http://192.168.138.123:8081/repository/maven-snapshots/</url>
    </snapshotRepository>
</distributionManagement>


</project>
```
4) Here we have configured the url next we need to configure password
5) We need to install plugin called config file provider (this is for nexus ) as it is 3rd party we need install it to get the settings
6) Got to manage-jenkins --> Managed file
7) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/8ba89eb1-3e4a-47c1-9435-aa24a5f870dd)
8) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/714b2510-d9cb-4fd4-959b-c1fb2e322c27)
9) Rest all settings are same  click on next
10) create new config --> ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/29d74229-c49f-49e7-8347-a5800ebcc50b)
11) uncomment servers and add username and password of nexus repo in the CONTENT 
```
 <server>
      <id>maven-releases</id>
      <username>admin</username>
      <password>idrbt</password>
    </server>
    
    <server>
      <id>maven-snapshots</id>
      <username>admin</username>
      <password>idrbt</password>
    </server>
```
12) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/597229a8-a63d-4228-bcbb-6c1e9d79f897)
13) Check the nexus repo to find the artifacts
14) Trivy is used for file system scan as well as docker image
15) 
