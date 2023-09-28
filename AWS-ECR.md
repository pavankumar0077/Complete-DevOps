# AWS Elastic Container Registry

- Elastic : It is scalable and available in nature -- Increase the capacity 
- Container : A pkg which container the application code and it's dependencies to run the application.
- Registry : Container Registry -- (Similary to DockerHub, Quay.io or GCR) -- Used to store the docker images

Difference b/w ECR & Dockerhub
--
![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/f783c500-8f5e-4b9f-9f30-0bcb0a151ce1)

- Dockerhub -- It is public and free to use, we have options like public repos and private repos
- Dockerhub -- User have to register with their own account
- ECR -- It supports private repos, bydefault it will create private repo
- ECR -- IAM USERS integrate with ECR
- ECR -- Have good integration with other AWS services like EKS

Create ECR
--
1) Search for ECR ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/6e04dfcf-ff71-43a1-9cc6-d885d8419563)
2) Image scan ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/2e859236-600d-4893-84db-87c4d3cbc8c2)

Push images to ECR
--
1) WE NEED AWS CLI installed in the system
2) Link ``` https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html ```
3) Click on Repo and click on view push commands
4) ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/9a208e5d-e349-4229-8ac5-bc504bad57a7)
5) ECR login usig AWS CLI - Similar to docker login using CLI ![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/7601f709-b1cf-4b76-b2ec-0bbc095bcc2a)
6) 
