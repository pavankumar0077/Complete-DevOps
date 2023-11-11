# Explain Devops project in interview

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/6af49d48-8733-453e-9d1b-b08cb9b3360c)

1) Client : There is a request from client that change the background and font style of the application
2) Manager : Mananger create a JIRA ticket and that is assigned to a Developer
3) Developer : Developer do the change and test it in his local machine or in dev env
4) Github : Pushes into Github repo, by creating new branch and dev will raise a PR for his changes to reflect in master or main branch... (From here a DEVOPS ENGINEER WORK WILL START)
5) Jenkins : STAGE 1 : Devops engineers will compile the source code and test the unit test cases, Check whether there are any errors like (syntax, test cases) failures and etc, If everything
works fine, code will be moved to next stage if not pipeline will be failed.
6) SonarQube : It will check if the quality of code is good or not, code smells, code coverage
7) Maven : Here artifact will be created jar or war of the application it means packing of the application.
8) OWSAP Dependency Check : We run a DC check Vulnerabilities on source code, dependencies and libraries as well as jar file.
9) Nexus or JForg repository : Here we are deploying our artifact in arficatory manager.
10) Docker : Here we create the dockerfile and build docker image and we scan the docker image
11) Trivy : Here created docker image is scanned be'coz the docker is layered scanning is the docker image is good security pratice
12) DockerHub : After scanning the doker image we will push the docker image into dockerhub repo.
13) Kubernetes : We create manifest files to deploy our applicaiton in kubernetes cluster.
14) Monitoring : We will monitor our application 24/7 using prometheus and grafana.
15) PenTesting : We use OWASP ZAP for pin testing, It will do attacks on our application from our side only to test what are the vulnerability points on our application from ehre
attacks can be done, once we find out we will inform developers and they will fix it.
