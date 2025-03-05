It runs one copy of your pod
on each node in your cluster.

![image](https://github.com/user-attachments/assets/9a843ecd-d32b-4662-8d03-96817486f191)


The DaemonSet ensures that one copy of the pod
is always present in all nodes in the cluster.
So what are some use cases of DaemonSets?
Say you would like to deploy a monitoring agent
or log collector on each of your nodes in the cluster,
so you can monitor your cluster better.
 
The kube-proxy component can be deployed
as a DaemonSet in the cluster.

![image](https://github.com/user-attachments/assets/20170734-337c-4655-81f8-f02850734380)

![image](https://github.com/user-attachments/assets/2e4e19df-d052-40d2-985d-84e8777f5bad)
