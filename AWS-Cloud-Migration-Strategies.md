# AWS CLOUD MIGRATION STRATEGIES

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/0f76b7d2-86f8-4c48-b998-7012de01fc24)
![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/ff317c2a-56fd-488c-ae46-627c7b0f7cfc)
![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/78a569bc-3260-4256-89e4-3db4cb83a5ba)
![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/1ef3fe7c-d045-4fe1-a804-fb631355eddc)

Step 1 - Preparation:
--
- if an organization having more than 200 micro services or having microservices --- Then Preparation stage is done
- or if an organization is having monolithic application and application developers and DevOps engineers are planning to convert into microservices
- If we are converting monlithic application then Preapreation stage is needed.

### NOTE : Preparation and Planning is one time activity

Step 2 - Planning - Phase:
--
- in the above as we have 200 plus micro services and we need like 20 micro services or 50 micro services should be in the phase one
- that means this micro services are like important or it is like critical resource that it should be released in phase five itself
- here we are doing this micro services into like from on premise servers to cloud platform
- and rest of the micro services will be released or like move to cloud in phases different different phases
- like phase one phase two phase three and phase five like that depends on the organization

Step 2 - Planning - Strategies:
--
We have seven different migration strategies (REHOST, REFACTOR AND REPLATFORM ) - Mostly used
--
REHOST
--
1) Rehost means it is simply like lift and shift for example we have micro services that are in on premise or private cloud and that are moved to  AWS or public cloud
2) Ex: let's say for an example we have Kubernetes cluster in on premise server which has three namespaces and 10 pods
3)  which are running so now what we have to do is like in the AWS cloud we create a Kubernetes cluster with three namespaces and we'll deploy or will try to run the parts here like deployment and the same
4)  infrastructure like same how it is running in the on premise it will be running in the same like on premise servers
5)  this lift and shift mechanism will work perfectly if you don't have any dependency on OS or hardware or environment
6)  Most popular migration strategy - Create similar env in the AWS

REPLATFORM
--
1) Replatform is nothing but like we are moving to cloud AWS cloud but now we are availing from servives
2) for example if you have a cumulative cluster then we are creating that Kubernetes cluster like control plane like
3) master node in different different availability zones so that we have high availability now like master node will be in the very high high availability zones.

REFACTOR/REARCHITECTURE
--
1) this strategy is very much suitable like if we have monolithic application and now we want to convert that application into microservices
2) at that time we have to refactor it or like rearchitecture it with the latest technologies to convert into microservices
3) so we will choose best services which are offered by AWS to convert monolithic to microservices

RELOCATE
--
1) relocate is nothing but we are moving our Kubernetes cluster on premise 1 to a WS I like using ES or openshift or Rosa ( Red Hat open shift on AWS)
2) relocate NBC costly as well and we have to be very careful because we are migrating to cloud
3) we are changing our platform completely

RETAIN
--
1) so retail is nothing but like we are planning to migrate our microservice applications to AWS in different different phases
2) in between like like we have 10 to 20 micro services where we thought like this applications should be or can be can be in the on premise itself
3) because they are very secure like it contains user information and other stuff
4) So what we planned is like we can connect this applications to the AWS migrated applications to communicate and run the applications.
5) There will one gateway to connect to the secure applications which are present in the on prem server

RETIRE
--
1) so we have migrated some of the applications but for later point we have realized that in ten of the applications are
2) no one is using so at that time we are retaining the 10 applications which are not using

REPURCHASE
--
1) let's say for example we are using VM that's in an on Prem servers and in AWS we are looking for the same like VMware or
2) we are looking for some of the best futures that AW provide instead of VMware



Step 3 - Migration
--
- As part of the migration we will take planning - phases like phase 1 ... phase 2 and we will migrate it to AWS cloud

Step 4 - Monitor
--
1) let's say we have migrated our applications like in the phase one we have migrated 50 applications like 50 micro services
2)  we have to monitor it for one month two months depending upon criticality of the applications
3)  here we will do like we will give this application access to some beta customers or internal employees to check the performance and monitor the applications how they are working before and after the migration

Step 5 - Improve/Optimize
--
1) optimize is like one time activity where we choose to do only at once
2) we have to evaluate whether there is any advantage or not by doing migration
3) we will evaluate and we will find achievements are like the cost optimization and etc
4) after the evaluation we will optimize like for COPD optimization we will create some jira tickets and will try to optimize it more.
