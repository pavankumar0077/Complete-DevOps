Multiple Scheduler

So you decide to have your own scheduling algorithm

to place pods on nodes

so that you can add your own custom conditions

and checks in it.



When creating a pod or a deployment,

you can instruct Kubernetes

to have the pod scheduled by a specific scheduler.



![image](https://github.com/user-attachments/assets/f6ec6765-1763-4b16-a071-f3b760e60a62)




![image](https://github.com/user-attachments/assets/96483be8-0d6b-43e9-925c-ebeafec59946)




This is not how you would deploy a custom scheduler 99%

of the time today because with kubeadm deployment,

all the control plane components run

as a pod or a deployment within the Kubernetes cluster.



Custom Scheduler: Deploy a custom scheduler for specific workloads, such as real-time applications requiring low-latency scheduling.
Bastion Host: Use a bastion host to create an SSH tunnel for accessing RDS instances in private subnets.
Annotations: Annotate pods to specify which scheduler should handle them, ensuring appropriate scheduling based on workload requirements.
Resource Efficiency: Multiple schedulers improve resource utilization and scalability by handling diverse workloads efficiently.

 

![image](https://github.com/user-attachments/assets/07d1c057-18c0-4ce6-9f21-1488a094d85b)




how do you know which scheduler picked it up?

So we have multiple schedulers.

How do you know which scheduler picked up

scheduling a particular pod?



![image](https://github.com/user-attachments/assets/02228764-1c82-4916-b010-52ccd228b3f1)




![image](https://github.com/user-attachments/assets/250aef04-e7ef-488f-b50d-9bc798f7a163)
