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


