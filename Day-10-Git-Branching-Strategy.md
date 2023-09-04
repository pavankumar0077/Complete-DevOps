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
![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/54a5a5e2-dda1-43d9-8374-3a110f174049)


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
8)
```


