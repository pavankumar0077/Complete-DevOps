If any taints exist on the node node01 
To create a taint on node01 with key spray, value of mortein and effect of noSchedule



Kubectl describe node node01 | grep "Taints"

kubectl taint node node01 spray=mortein:NoSchedule



Node Selector:

==========

![image](https://github.com/user-attachments/assets/c4b164a0-ad4c-430a-8ffc-21955646096b)

![image](https://github.com/user-attachments/assets/4500cb6b-0eea-4843-8aa4-063bdccc1668)




Node selectors served our purpose, but it has limitations.

We used a single label and selector

to achieve our goal here.



For example, we would like to say

something like place the pod on a large or medium node,

or something like place the pod on any notes

that are not small.

You cannot achieve this using node selectors.

For this, node affinity and anti-affinity features



With node selector we can not have the advanced features like or, and expressions like that we can achieve this by using node affinity 

Node affinity:
=========
The node affinity feature

 
![image](https://github.com/user-attachments/assets/8c6e4e33-68a2-42a1-a4d0-f139857f4fd2)


provides us with advanced capabilities

to limit pod placement on specific nodes.

With great power comes great complexity,

so the simple node selector specification

will now look like this with node affinity

although both does exactly the same thing.

Place the pod on the large node.



![image](https://github.com/user-attachments/assets/6bc03a62-915c-4593-9de5-b3bf28f7e584)




![image](https://github.com/user-attachments/assets/dff2b4de-cb82-44e3-8017-fc4e5f8e60f4)




![image](https://github.com/user-attachments/assets/675cb0e6-30c5-4196-9dec-0f977e31ca4d)




The exist operator will simply check

if the label size exists on the nodes,

and you don't need the value section for that

as it does not compare the values.



![image](https://github.com/user-attachments/assets/cf3e74c9-543a-4038-966f-a3d4ecd034f9)




Now what would happen to the pods

that are running on the node?

As you can see, the two types of node affinity

available today has this value set to ignored,

which means pods will continue to run

and any changes in node affinity

will not impact them once they are scheduled.

The new types expected in the future

only have a difference in the during execution phase.

A new option called required during execution is introduced

which will evict any pods that are running on nodes

that do not meet affinity rules.

In the earlier example, a pod running on the large node

will be evicted or terminated

if the label large is removed from the node.





How many lables exists in node node01 ?
Apply a label color=bule to node node01
Check the taints are presents on the nodes or not 





kubectl get node node01 --show-labels


kubectl lable node node01 color=blue

kubectl describe nodes | grep -A 5 "Taints"
kubectl describe nodes controlplane node01 | grep -A 5 "Taints"


 


 

