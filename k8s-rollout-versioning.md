Rollout & Versioning



When you first create a deployment, it triggers a rollout.

A new rollout creates a new deployment revision,

let's call it revision one.

In the future when the application is upgraded,

meaning when the container version is updated to a new one,

a new rollout is triggered,

and a new deployment revision is created,

named revision two.



deployment and enables us to roll back

to a previous version of deployment if necessary



TO GET THE HISTROY OF REVISION OF THE DEPLOYMENT 

![image](https://github.com/user-attachments/assets/964578da-4f85-41ec-9494-d0800bed052e)




-- if you do not specify a strategy

while creating the deployment,

it will assume it to be rolling update.

In other words, rolling update

is the default deployment strategy.



![image](https://github.com/user-attachments/assets/0b189284-ac18-4e00-ae72-899bc3ef809a)



--> But there is another way to do the same thing.

You could use the kubectl set image command

to update the image of your application,

but remember, doing it this way will result

in the deployment definition file



![image](https://github.com/user-attachments/assets/96517dbd-b9ca-486c-bf3d-f6debecd1932)







```
kubectl describe deployment my-deployment 
- To check the recreation or rolling update or any other info abt the deployments
```

When a new deployment is created,

say, to deploy five replicas,

it first creates a replica set automatically,



Kubernetes deployment object creates a new replica set

under the hood and starts deploying the containers there,

at the same time, taking down the pods

in the old replica set

following air rolling update strategy.



![image](https://github.com/user-attachments/assets/589b2852-bad8-44d6-af61-94f55fc07220)




---> Say for instance, once you upgrade your application,

you realize something isn't really right.

Something's wrong with the new version

of the build you used to upgrade,

so you would like to roll back your update.

Kubernetes deployments allow you to roll back

to a previous revision.

To undo a change



![image](https://github.com/user-attachments/assets/ad270d51-3afd-403e-b07c-564382003476)




![image](https://github.com/user-attachments/assets/f4d83aef-a277-4076-a2fd-19b78cf2efa5)




To update the image to version v2 without re-creating the deployment, you can use the following command:
 




```
kubectl set image deployment/frontend simple-webapp=kodekloud/webapp-color:v2 --record
```
