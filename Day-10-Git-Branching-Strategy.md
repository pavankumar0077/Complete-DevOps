# Git Branching Strategy

```
Branch -- Seperation (web appn) -- Introduce new changes in the web appn (V2)
Version one have basic feature, Version two has advanced features in web-app.
1) Here instead of making changes directly to master or main branch
2) New create a new branch for advance changes liek feature-branch, develop-01 etc names
3) Test the code with new changes in new branch.
4) After testing and done and application is working fine then we will merge the V2 features branch
in main or master branch. (Existing functionality)
5) If we want we can delete the feature branch now
6) Why we are creating the branch is to make sure that adding changes should not
affect the existing functionality. V1
```

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/99045937-13d4-4c7f-ad67-636387b0aefc)

Good Braching Strategy
--
![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/87e03b97-95bd-48c9-ad94-bb08c10e7f10)

```
1) We have Calculator web-app in Github repo,You have developed cal application with some
fixes. Like customer is saying there are some issues, So everyday we work on it and
and fix the issues like creating more and more commits on the existing github.
2) All of the sudden some dev's wanted to add new features to the existing calculator appliication
3) Instead of giving the same branch. And the features is very big and it takes lot of time
4) It make affect the existing code that's why we create new branch like feature-advancement.. and
dev's are working on it.
5) And when we are confident that feature branch is working smoth after testing
we will merge this feature branch into main or master branch.
6) Similarly multiple dev's are there with new featuers  and there will be multiple request you may create a number of feature branches
7) For this reason we have multiple features branches and finally they are all added to the master or main branch
8) **Release Branch** - Any application should be delivered to the customers. Instead of building the appn for master. We usually build fromt he release branch. Now all the features branch code is working fine.
9) Now we create a release branch and now we delivery this branch to the customer build and ship this branch to customer.
```

Why from release branch why not from master branch
--
1) Because master is usually kept for active development, So there can be dev's how actively work on master
2) Testing we are testing the release branch or you are running some E2E or functionality tests on your release branch you dont want somebody to actively get new changes onto this release branch because  you are testing. At this point you
want to say that okay all changes are good and i want to deliver these customer.So you don't want any new changes to go the customer.
3) You create a release branch and you say that the development of this branch is done now this branch is only there for testing and deliver to the customer. Like this same process will repeat.

Ex: Kubernetes also uses the same strategy

Hot Fix Branch
--
1) Let's say a customer is getting critical issue on the prod and you want to fix that code immediately in such cases
2) We will create hot fix branches and makes changes ensure that is working fine and tested and we deliver that hot fix branches to master as well as release branch and we will ship the release branch code to the customer.

4 types of branches
--
1) Master/Main branch
2) Feature branch
3) Release branch
4) Hot fix branch (Both master and release should be in both)
**NOTE: ANY BRANCH CHANGES SHOULD BE IN MASTER BRANCH**




