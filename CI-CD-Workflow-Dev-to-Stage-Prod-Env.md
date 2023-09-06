# CI/CD Workflow from Dev to Stage to Prod Env

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/f6f302da-37b4-43a8-93c3-1c7febf27773)
THIS IS ONLY FOR DEV ENV.

1) Their is one dev and we want to do some code changes in feature branch of this VCS github
2) In github there will be a webhook that will trigger Jenkins pipeline, 1st thing in jenkins pipeline is
CI where there would be different stages such as code checkout stage, building and unit testing (maven build too)
next stage is code scanner stage we will verify static code, code smells we use sonarQube for this. after this
we will move to image build creation stage docker,build or podman, once the container build is created  in the same
jenkins pipeline we will scan the container image that is created for image scanning we use trivi, snike clair and now
we will move to the final stage in CI that is pushing the image to image registry we have dockerhub or quary artifactory
nexus, Jfrong all of these stages are in CI
3) Once we have new image test:V2, CD part update the kubernetes manifest yaml file, deployment.yaml or statefull set or
deamon set. which the new version good pratice is to keep manifest in different git repository.
4) We will update new version in deployment.yaml file we can also use Helm charts and update to new version using
values.yaml in helm chat and then we can use advance deployment tools or delivery tools such are ARGO CD or flux
or Spinnaker basically use GITOPS approach to deploy on our kubernetes cluster.

BRANCHING STRATEGY IS THE KEY
--
![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/619e0121-ebee-4a2a-8e4a-a7f71f218bb7)

1) In GIT repo we create different kinds of branches
2) FEATURE BRANCHERS -- enchancement 1 (branch - team1), new tables(new barnch team2), UI (branch team3)
3) MAIN BRANCH - single branch -- active development will be going on. (small changes)
4) RELEASE BRANCHES -- Differnet versions -- in PORD
5) HOT FIX -- Customer issues (quick issues)

CI CD FLOW
--
![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/82788d54-d6f3-42ed-9037-4631b241a61d)

1) DEV (working on it) ---> commited the changes to feature Git repo (payment app) and it will trigger jenkins pipeline using
webhooks using ARGO CD it runs all the stages and finally deploys in developer kubernetes cluster assigned for this.
2) Now from feature branch the change has to go to the master branch or Main. -- other webhook that is configured for main branch
It would trigger a different jenkins pipeline --> similary to features. Only difference is now the feature branch
changes merge into main branch in thsi branch we have a active development code. using jenkins we run all the stages that are configured
and we integrate it will ARGO CD and deploy it on a stagging env anf for this staggin env we have a different kubernets cluster.
In some organization they use same kubernetes cluster but they use different namespaces. (not a good process)
3) In this stagging kuberneres env. QA team or Automated jenkins tests they can run additional manual tests.
perrformance test, DDOS attack, pen testing and etc( along with automation test) we also do this tests.
5) Finally devops creates a RELEASE BRANCH from the main branch again jenkins pipeline (release pipelin)(IMP) -- stages are runned using ARGO CD we deploy
to Pro-Prod or UAT env we will verify and everything working we will promote to PROD env
6)  All of these stage good pratice good to have different  kubernetes cluster for each ENV.
7)  In some cases we can have dev and stagging can be same kubernetes cluster different namespaces.

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/4e943ec5-09c1-42ca-9582-11457e770f93)



 

   
