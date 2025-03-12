Monitoring:






```
kubectl top node
kubectl top pod
```

Gives the Metrics, we need to install metrics server from github and we need to apply the files 
It will create some pods, and roles to access the metrics.

 

![image](https://github.com/user-attachments/assets/a8c698a6-288e-4e0a-9407-c211aa88c3fa)




IF we want to apply the files, create resources we can directly get the files from github like this 




```
 kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
```

![image](https://github.com/user-attachments/assets/e134b33e-77e0-4a48-907f-bbccab7d4605)



![image](https://github.com/user-attachments/assets/5ff27d78-d3ef-4358-b4b3-d5dc2fab61f6)




-> If there are multiple containers within a pod,

you must specify the name

of the container explicitly in the command.

Otherwise it would fail asking you to specify a name.



If the pod contains more than 1 image then we need to specify the name to check the logs 

![image](https://github.com/user-attachments/assets/51863079-98dc-46ba-81cd-079ffa42f8c9)
