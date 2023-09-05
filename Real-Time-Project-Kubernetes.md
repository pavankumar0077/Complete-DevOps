# 

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/88a116d3-3a8e-4cd4-9dd4-859210e13ae2)

```
1) Dev --> Code (Dockerfile)
2) Pushes to GitHub (changes or new commit)
3) Jenkins -- Webhook will notifies the changes to jenkins (pulls the new code)
4) Jenkins will ssh to ANSIBLE server
5) Ansible server will building the image based on dockerfile (1st task of ansible server)
6) Once the image is build it will tag it and push to the dockerhub
7) 2nd task is it will ssh to kubernetes cluster server and ansible will run the playbook so that it will run
kubectl command on web app and it will try to fetch the least image it will pull from dockerhub and start
building the image, from image it will build container. That conatiner should be accessable using the IP and port
which we are going to enable by writing service.yaml file in the kubernetes cluster
```



