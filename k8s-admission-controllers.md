admission controllers.



KUBECTL -- API SERVER -- RESOURCE CREATED (POD) -- DETAILED STORE IN ETCD
 

![image](https://github.com/user-attachments/assets/8c58d6ad-505f-42cb-9588-d9d54157f5a7)




![image](https://github.com/user-attachments/assets/3bd30628-7ec8-4911-ae91-448ff74d8522)




most of these rules that you can create

with role-based access control,

is at the Kubernetes API level,

and what user is allowed,

access to what kind of API operations.

And it does not go beyond that.

![image](https://github.com/user-attachments/assets/bde2f536-3429-4e76-9a6d-62132338c0b9)




NOTE : BEFORE SENDING THE REQUEST TO API-SERVER ADMISSION CONTROLLER WILL REVIEW THE REQUEST AND CHECKS AND PROCESS IT



For example, when a pod creation request comes in,

you'd like to review the configuration file

and look at the image name

and say that you do not want

to allow images from a public docker hub registry,

only allow images from a specific internal registry.

Or to enforce

that we must never use the latest tag for any images.

Or say, for example, you'd like to say,

if the container is running as the root user,

then you do not want to allow that request

or allow certain capabilities only

or to enforce that the metadata always contains labels.



![image](https://github.com/user-attachments/assets/38153c38-f32b-4785-b369-60c75c8b7014)




![image](https://github.com/user-attachments/assets/ff2a966c-79d3-48bb-bb2f-50a0f78b26c3)




Namespace Auto Provision Admission Controller. This will automatically create the namespace if it does not exist,

 

![image](https://github.com/user-attachments/assets/d913337e-5b34-45c9-afa6-b93420ae04a3)




![image](https://github.com/user-attachments/assets/9e6856c8-b43f-452d-bc00-316200840cbe)




Note that the Namespace Auto Provision

and the Namespace Exists at admission controllers

are deprecated and is now replaced

by the Namespace Lifecycle Admission Controller.



kube-apiserver is running as pod you can check the process to see enabled and disabled plugins.





``` ps -ef | grep kube-apiserver | grep admission-plugins ```
