# JENKINS CICD TO DEPLOY THE APPLICATION IN K8'S CLUSTER.

```
pipeline {
    agent any
    
    environment {
        JAVA_HOME = '/path/to/java'
        MAVEN_HOME = '/path/to/maven'
        SONAR_HOME = '/path/to/sonar-scanner'
        SNYK_TOKEN = credentials('snyk-api-token')
        DOCKER_REGISTRY = 'your-docker-registry.com'
        DOCKER_CREDENTIALS = credentials('docker-credentials-id')
        GIT_CREDENTIALS = credentials('git-credentials-id')
        APP_NAME = 'your-app'
        GIT_REPO_URL = 'https://github.com/your-org/your-k8s-manifests.git'
        GIT_BRANCH = 'main'
        PROMETHEUS_CONFIG_REPO = 'https://github.com/your-org/prometheus-config.git'
    }
    
    stages {
        stage('Checkout Application Code') {
            steps {
                checkout scm
            }
        }
        
        stage('Build') {
            steps {
                sh "${MAVEN_HOME}/bin/mvn clean package"
            }
        }
        
        stage('Unit Tests') {
            steps {
                sh "${MAVEN_HOME}/bin/mvn test"
            }
            post {
                always {
                    junit '**/target/surefire-reports/*.xml'
                }
            }
        }
        
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh "${SONAR_HOME}/bin/sonar-scanner"
                }
            }
        }
        
        stage('Snyk Security Scan') {
            steps {
                snyk(
                    command: 'test',
                    projectName: "${APP_NAME}",
                    organisation: 'your-org-name',
                    failOnIssues: false,
                    snykInstallation: 'snyk'
                )
            }
        }
        
        stage('Build and Push Docker Image') {
            steps {
                script {
                    docker.withRegistry("https://${DOCKER_REGISTRY}", DOCKER_CREDENTIALS) {
                        def appImage = docker.build("${DOCKER_REGISTRY}/${APP_NAME}:${BUILD_NUMBER}")
                        appImage.push()
                        appImage.push('latest')
                    }
                }
            }
        }
        
        stage('Update Kubernetes Manifests') {
            steps {
                script {
                    git branch: GIT_BRANCH, credentialsId: GIT_CREDENTIALS, url: GIT_REPO_URL
                    sh "sed -i 's|${DOCKER_REGISTRY}/${APP_NAME}:.*|${DOCKER_REGISTRY}/${APP_NAME}:${BUILD_NUMBER}|' k8s/staging-deployment.yaml"
                    sh "sed -i 's|${DOCKER_REGISTRY}/${APP_NAME}:.*|${DOCKER_REGISTRY}/${APP_NAME}:${BUILD_NUMBER}' k8s/production-deployment.yaml"
                    sh "git config user.email 'jenkins@example.com'"
                    sh "git config user.name 'Jenkins'"
                    sh "git add k8s/staging-deployment.yaml k8s/production-deployment.yaml"
                    sh "git commit -m 'Update image to ${BUILD_NUMBER}'"
                    sh "git push origin ${GIT_BRANCH}"
                }
            }
        }
        
        stage('Deploy to Staging') {
            steps {
                script {
                    sh "kubectl apply -f k8s/staging-deployment.yaml"
                    sh "kubectl rollout status deployment/${APP_NAME}-staging -n staging"
                }
            }
        }
        
        stage('OWASP ZAP Scan') {
            steps {
                script {
                    docker.image('owasp/zap2docker-stable').inside('--network="host"') {
                        sh 'zap-baseline.py -t http://your-staging-url -r zap-report.html'
                    }
                }
            }
            post {
                always {
                    publishHTML([allowMissing: false, alwaysLinkToLastBuild: true, keepAll: true, reportDir: '.', reportFiles: 'zap-report.html', reportName: 'ZAP Security Report'])
                }
            }
        }
        
        stage('Approval for Production') {
            steps {
                input "Deploy to production?"
            }
        }
        
        stage('Deploy to Production') {
            steps {
                script {
                    sh "kubectl apply -f k8s/production-deployment.yaml"
                    sh "kubectl rollout status deployment/${APP_NAME}-prod -n production"
                }
            }
        }
        
        stage('Update Prometheus Config') {
            steps {
                script {
                    git branch: 'main', credentialsId: GIT_CREDENTIALS, url: PROMETHEUS_CONFIG_REPO
                    sh """
                    cat <<EOF >> prometheus.yml
                    - job_name: '${APP_NAME}'
                      kubernetes_sd_configs:
                      - role: pod
                      relabel_configs:
                      - source_labels: [__meta_kubernetes_pod_label_app]
                        regex: ${APP_NAME}
                        action: keep
                    EOF
                    """
                    sh "git add prometheus.yml"
                    sh "git commit -m 'Add scrape config for ${APP_NAME}'"
                    sh "git push origin main"
                    
                    // Reload Prometheus config
                    sh "curl -X POST http://prometheus-url:9090/-/reload"
                }
            }
        }
        
        stage('Update Grafana Dashboard') {
            steps {
                script {
                    // Use Grafana API to update or create a dashboard
                    // This is a placeholder and would need to be implemented based on your specific dashboard requirements
                    sh "curl -X POST -H 'Content-Type: application/json' -d @dashboard.json http://grafana-url/api/dashboards/db"
                }
            }
        }
    }
    
    post {
        always {
            cleanWs()
        }
        success {
            script {
                slackSend(color: 'good', message: "Build Successful: ${env.JOB_NAME} ${env.BUILD_NUMBER}")
                emailext (
                    subject: "Build Successful: ${env.JOB_NAME} ${env.BUILD_NUMBER}",
                    body: "Your build completed successfully. Check console output at ${env.BUILD_URL}",
                    to: 'team@yourdomain.com'
                )
            }
        }
        failure {
            script {
                slackSend(color: 'danger', message: "Build Failed: ${env.JOB_NAME} ${env.BUILD_NUMBER}")
                emailext (
                    subject: "Build Failed: ${env.JOB_NAME} ${env.BUILD_NUMBER}",
                    body: "Your build failed. Check console output at ${env.BUILD_URL}",
                    to: 'team@yourdomain.com'
                )
            }
        }
    }
}
```
```
Tools Used in the Pipeline:

Jenkins
Java/Maven
Docker
Kubernetes (kubectl)
SonarQube
Snyk
OWASP ZAP
Git
Prometheus
Grafana
Slack/Email for notifications

Step-by-Step Setup Process:

Jenkins Setup:

Install Jenkins on your server or use a cloud-hosted solution.
Navigate to "Manage Jenkins" > "Manage Plugins" and install these plugins:

Pipeline
Git
Docker Pipeline
Kubernetes CLI
SonarQube Scanner
Snyk Security
OWASP ZAP
Slack Notification
Email Extension




Java and Maven:

Install Java and Maven on your Jenkins server.
In Jenkins, go to "Manage Jenkins" > "Global Tool Configuration".
Add JDK installation, specifying the path to your Java home.
Add Maven installation, either by letting Jenkins install it automatically or specifying the path to your Maven installation.


Docker:

Install Docker on your Jenkins server.
Add the Jenkins user to the docker group: sudo usermod -aG docker jenkins
Restart Jenkins: sudo systemctl restart jenkins


Kubernetes (kubectl):

Install kubectl on your Jenkins server.
Configure kubectl to connect to your cluster (usually by placing the kubeconfig file in ~/.kube/config).
Ensure the Jenkins user has access to this config file.


SonarQube:

Set up a SonarQube server (can be on a separate machine).
In Jenkins, go to "Manage Jenkins" > "Configure System".
Find "SonarQube servers" section and add your SonarQube server details.
Generate a token in SonarQube and add it as a credential in Jenkins.


Snyk:

Sign up for a Snyk account and get your API token.
In Jenkins, go to "Manage Jenkins" > "Manage Credentials".
Add a new Secret text credential with your Snyk API token.


OWASP ZAP:

No additional setup needed as we're using the Docker image in the pipeline.


Git:

Ensure Git is installed on your Jenkins server.
In Jenkins, go to "Manage Jenkins" > "Manage Credentials".
Add credentials for your Git repositories (username/password or SSH key).


Prometheus:

Set up Prometheus in your Kubernetes cluster.
Ensure Prometheus has permissions to scrape metrics from your applications.


Grafana:

Set up Grafana in your Kubernetes cluster.
Configure Grafana to use Prometheus as a data source.


Slack/Email Notifications:

For Slack:

Create a Slack app and get the webhook URL.
In Jenkins, go to "Manage Jenkins" > "Configure System".
Find "Slack" section and add your team subdomain and integration token.


For Email:

In Jenkins, go to "Manage Jenkins" > "Configure System".
Find "E-mail Notification" section and configure your SMTP server details.




Pipeline Setup:

In Jenkins, create a new Pipeline job.
In the job configuration, under "Pipeline", select "Pipeline script from SCM" if your Jenkinsfile is in your repository, or paste the script directly if not.
If using SCM, provide your repository details and the path to your Jenkinsfile.


Environment Variables:

Update the environment variables in the Jenkinsfile with your specific values:

JAVA_HOME
MAVEN_HOME
SONAR_HOME
DOCKER_REGISTRY
APP_NAME
GIT_REPO_URL
PROMETHEUS_CONFIG_REPO




Credentials:

In Jenkins, go to "Manage Jenkins" > "Manage Credentials".
Add credentials for:

SNYK_TOKEN
DOCKER_CREDENTIALS
GIT_CREDENTIALS


Use the credential IDs in your Jenkinsfile.


Kubernetes Manifests:

Prepare your Kubernetes manifest files (staging-deployment.yaml, production-deployment.yaml) and push them to the repository specified in GIT_REPO_URL.


Prometheus and Grafana Configuration:

Prepare your Prometheus configuration repository (specified in PROMETHEUS_CONFIG_REPO).
Create a Grafana dashboard for your application (you'll need to implement the specifics in the "Update Grafana Dashboard" stage).



Final Steps:

Review and adjust the Jenkinsfile to match your exact requirements and infrastructure setup.
Run the pipeline and troubleshoot any issues that arise.
Set up proper access controls in Jenkins to restrict who can run the pipeline, especially the production deployment stages.
```

# JENKINS PIPELINE TO DEPLOY THE APPLICATION IN K8'S CLUSTER USING ARGO CD WITH ARGO IMAGE UPDATER.

```
pipeline {
    agent any
    
    environment {
        JAVA_HOME = '/path/to/java'
        MAVEN_HOME = '/path/to/maven'
        SONAR_HOME = '/path/to/sonar-scanner'
        SNYK_TOKEN = credentials('snyk-api-token')
        DOCKER_REGISTRY = 'your-docker-registry.com'
        DOCKER_CREDENTIALS = credentials('docker-credentials-id')
        APP_NAME = 'your-app'
        ARGOCD_SERVER = 'https://argocd.your-domain.com'
        ARGOCD_CREDENTIALS = credentials('argocd-credentials-id')
    }
    
    stages {
        stage('Checkout Application Code') {
            steps {
                checkout scm
            }
        }
        
        stage('Build') {
            steps {
                sh "${MAVEN_HOME}/bin/mvn clean package"
            }
        }
        
        stage('Unit Tests') {
            steps {
                sh "${MAVEN_HOME}/bin/mvn test"
            }
            post {
                always {
                    junit '**/target/surefire-reports/*.xml'
                }
            }
        }
        
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh "${SONAR_HOME}/bin/sonar-scanner"
                }
            }
        }
        
        stage('Snyk Security Scan') {
            steps {
                snyk(
                    command: 'test',
                    projectName: "${APP_NAME}",
                    organisation: 'your-org-name',
                    failOnIssues: false,
                    snykInstallation: 'snyk'
                )
            }
        }
        
        stage('Build and Push Docker Image') {
            steps {
                script {
                    docker.withRegistry("https://${DOCKER_REGISTRY}", DOCKER_CREDENTIALS) {
                        def appImage = docker.build("${DOCKER_REGISTRY}/${APP_NAME}:${BUILD_NUMBER}")
                        appImage.push()
                        appImage.push('latest')
                    }
                }
            }
        }
        
        stage('Trigger ArgoCD Image Updater') {
            steps {
                script {
                    sh """
                    argocd login ${ARGOCD_SERVER} --username ${ARGOCD_CREDENTIALS_USR} --password ${ARGOCD_CREDENTIALS_PSW} --insecure
                    argocd app set ${APP_NAME}-staging --kustomize-image ${DOCKER_REGISTRY}/${APP_NAME}:${BUILD_NUMBER}
                    """
                }
            }
        }
        
        stage('Wait for Staging Deployment') {
            steps {
                script {
                    sh """
                    argocd app wait ${APP_NAME}-staging --health --timeout 300
                    """
                }
            }
        }
        
        stage('OWASP ZAP Scan') {
            steps {
                script {
                    docker.image('owasp/zap2docker-stable').inside('--network="host"') {
                        sh 'zap-baseline.py -t http://your-staging-url -r zap-report.html'
                    }
                }
            }
            post {
                always {
                    publishHTML([allowMissing: false, alwaysLinkToLastBuild: true, keepAll: true, reportDir: '.', reportFiles: 'zap-report.html', reportName: 'ZAP Security Report'])
                }
            }
        }
        
        stage('Approval for Production') {
            steps {
                input "Deploy to production?"
            }
        }
        
        stage('Promote to Production') {
            steps {
                script {
                    sh """
                    argocd app set ${APP_NAME}-production --kustomize-image ${DOCKER_REGISTRY}/${APP_NAME}:${BUILD_NUMBER}
                    argocd app sync ${APP_NAME}-production
                    argocd app wait ${APP_NAME}-production --health --timeout 300
                    """
                }
            }
        }
    }
    
    post {
        always {
            cleanWs()
        }
        success {
            slackSend(color: 'good', message: "Build Successful: ${env.JOB_NAME} ${env.BUILD_NUMBER}")
            emailext (
                subject: "Build Successful: ${env.JOB_NAME} ${env.BUILD_NUMBER}",
                body: "Your build completed successfully. Check console output at ${env.BUILD_URL}",
                to: 'team@yourdomain.com'
            )
        }
        failure {
            slackSend(color: 'danger', message: "Build Failed: ${env.JOB_NAME} ${env.BUILD_NUMBER}")
            emailext (
                subject: "Build Failed: ${env.JOB_NAME} ${env.BUILD_NUMBER}",
                body: "Your build failed. Check console output at ${env.BUILD_URL}",
                to: 'team@yourdomain.com'
            )
        }
    }
}
```

```
Jenkins

Core CI/CD orchestration tool


Java

Programming language for the application


Maven

Build automation and dependency management tool for Java projects


Docker

Containerization platform for building and pushing application images


SonarQube

Static code analysis tool for code quality and security checks


Snyk

Security scanning tool for vulnerabilities in dependencies and containers


ArgoCD

GitOps continuous delivery tool for Kubernetes


ArgoCD Image Updater

Component of ArgoCD for automating image updates in Kubernetes manifests


Kubernetes

Container orchestration platform (implied, as ArgoCD deploys to Kubernetes)


OWASP ZAP (Zed Attack Proxy)

Dynamic Application Security Testing (DAST) tool


Git

Version control system (implied, as we're using SCM checkout)


Slack

Communication tool used for notifications


Email

Used for notifications


Docker Registry

For storing and distributing Docker images



These tools cover various aspects of the CI/CD pipeline:

Source Control Management
Build and Compilation
Dependency Management
Containerization
Static Code Analysis
Security Scanning
Continuous Delivery
Dynamic Application Security Testing
Notifications and Alerts
```

```
ArgoCD Image Updater Integration:

Added a "Trigger ArgoCD Image Updater" stage that uses the ArgoCD CLI to update the image for the staging application.
In the "Promote to Production" stage, we use the same method to update the production application's image.


Wait for Deployment: Added stages to wait for the ArgoCD applications to sync and become healthy after image updates.

To set this up:

ArgoCD Image Updater Configuration:

Install ArgoCD Image Updater in your Kubernetes cluster.
Configure it to watch your Docker registry for new tags.


ArgoCD Application Setup:

Your ArgoCD applications should be configured to use Kustomize or a similar tool that allows for image updates.

apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: your-app-staging
  namespace: argocd
spec:
  project: default
  source:
    repoURL: https://github.com/your-org/your-k8s-manifests.git
    targetRevision: HEAD
    path: staging
    kustomize:
      images:
      - your-docker-registry.com/your-app:latest
  destination:
    server: https://kubernetes.default.svc
    namespace: staging

Kubernetes Manifests:

Your manifests should use a placeholder tag (like 'latest') that ArgoCD Image Updater will replace.


Jenkins Setup:

Ensure the ArgoCD CLI is installed on your Jenkins server.
Add ArgoCD credentials to Jenkins.
```
