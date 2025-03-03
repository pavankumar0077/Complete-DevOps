Labels & Selectors

===========
Kubernetes objects use labels and selectors internally
to connect different objects together.
For example, to create a replica set
consisting of three different pods,
we first label the pod definition and use selector
in a replica set to group the pods.
In a replica set definition file,
you will see labels defined in two places.
Note that this is an area
where beginners tend to make a mistake.
The labels defined under the template section
are the labels configured on the pods.
The labels you see at the top
are the labels of the replica set itself.



To get the labelled pods, Here env,tier and bu.
How many object are there in prod env, including pods, rs and other objects 
Identify the pod which is part of the prod env, the finance bu and of frontend tier ?


```
kubectl get pods --selector=env=dev
kubectl get pods --selector=env=dev --no-headers | wc -l (To get the count)

kubectl get all -l env=prod (wrong)
kubectl get all --selector=env=prod --no-headers | wc -l
kubectl get pods,rs,deploy,svc,cm,secret --selector=env=prod --no-headers | wc -l

kubectl get pods --selector=env=prod,bu=finance,tier=frontend
```



Annotations

========
Finally, let's look at annotations.
While labels and selectors are used
to group and select objects,
annotations are used to record other details
for informatory purpose.
For example, tool details like name, version,
build information, et cetera,
or contact details, phone numbers, email IDs, et cetera
that may be used for some kind of integration purpose.



