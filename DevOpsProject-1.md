
![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/6dec9515-ca99-4b14-baca-32d39f4c0b56)

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/fb383a4f-ce70-4ca6-9baa-e3970c786c35)


![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/080ec385-def9-4c86-9497-0802494b9807)

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/e7c01b2c-d604-4116-9dba-4a2a297f70b5)

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/cb50afa7-ad88-4f80-bafe-3b7fbbf7f955)

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/fe5393ea-b5be-44b9-98ed-a9f022241d99)

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/3560c0f1-972a-49b9-825e-c8737dbf5fae)

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/04989e6d-8c4d-49db-a63d-3c75336c797f)

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/882c0e6c-b0c5-496d-9d83-328b2a155f46)

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/f47de4d6-149f-40a3-b888-c9ff90350c5d)


To Change the hostname
--
``` hostnamectl set-hostname server1.example.com ```

All the code which jenkins is pull will be available on ``` /var/lib/jenkins/workspace ``` dir

To extra any tar file ``` tar -xvzf file-name.tar ```

To rename mv apache-8.9.3-maven to maven use ``` mv apache-maven-3.8.8 maven ```

To find a file in linux ```  find / -name jvm ```
Ex: ``` find / -name java-11* ```

use ``` init 6 ``` to restart the instance

To start the docker at runtime use ```chk config```

```  docker run -d --name tomcat-containier -p 8081:8080 tomcat ``` Here 8081 is the outside or external or hostmachine port and 8080 is the container port of tomcat

``` su dockeradmin ``` For switch usering in linux 
``` chown -R dockeradmin:dockeradmin docker ``` To Change owenership, Here dockeradmin is the username and docker is dir name
``` docker container prune ``` To delete all the stopped containers
``` docker image prune -a ``` To delete all images
```  git commit -am "updates-index-page.jsp" ``` Use it everytime we don't need to (add . - git add .) instead use this command add and commit at same time 

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/e248a775-fc51-405f-bb92-03df1b03a295)

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/1a90fce5-fb86-4dfa-a8ae-ad7dda7930b4)

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/a9dc0430-a71b-4f5f-8788-83f56880ee34)

1) We are using centos os as base image
2) and we need jave to run tomcat
3) and to create directory use mkdir
4) we want to go to tomcat dir use WORKDIR
5) Here to donwload files we use wget or add, if we use RUN then we must mention wget, and if we use ADD then no need to mention anything just ADD url-link



```
FROM tomcat:latest
RUN cp -R /usr/local/tomcat/webapps.dist/* /usr/local/tomcat/webapps
```
To run tomat without issue (403 - after updating the version of tomcat).The above dockerfile is customized and it works without error
 
