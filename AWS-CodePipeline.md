# AWS CODE PIPELINE

## IN GENERAL CI CD PROCESS (WITHOUT USING AWS CODE-PIPELINE)
![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/acc9b113-131a-4d85-bb29-09200b0c8241)
1) **jenkins -- implementing CI and invoking CD (ArgoCD)**


## CI CD PROCESS USING AWS CODE PIPLINE
1) AWS code pipeline -- Implementing CI and Implementing CD
2) CI -- is done by AWS CodeBuild
3) CD -- AWS CodeDeploy
4) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/5faf6752-d096-4e11-847d-11a0ae830c6c)

AWS CodePipeline is paid, Jenkins is Free, Why Organizations are chosing aws
--
1) Jenkins -- For example we have master slave architecture and, it is keep on increasing like code commit, pipeline and etc, For these we need more and more instance and there
shoulld be devops engineer should mangement all the instances for update or other things. We can use docker as agent, and even we can use auto scaling groups.
2) Codepipeline -- Here codepipeline will manage all the things for us. It is like manged service and pay as you go model. This is only for AWS provided only.
