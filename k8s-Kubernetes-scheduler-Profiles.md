Kubernetes scheduler Profiles:



So the first thing that happens is that

when these pods are created,

the pods end up in a scheduling queue.

So this is where the pods wait to be scheduled.

So at this stage, pods are sorted

based on the priority defined on the pods.

So in this case, our pod has a high priority set.

So, to set a priority,



4 Phases 



priority
Filtering
Scoring
Binding



![image](https://github.com/user-attachments/assets/e00d1cad-3a38-4474-a94f-b38b9661e7a2)




sorting happens in this scheduling phase.

Then, our pod enters the filter phase.

This is where nodes that cannot run the pod

are filtered out.

This is where nodes that cannot run the pod

are filtered out.



the scheduler associates a score to each node

based on the free space that it will have

after reserving the CPU required for that pod.

So, in this case, the first one has two left

and the second node will have six left.

So, the second node gets a higher score.



this is where the pod is finally bound to a node

with the highest score.



Note: In every phase we have lot of plugin used.

![image](https://github.com/user-attachments/assets/fb857d8a-4bbc-4ee7-b806-545343736f12)




write our own plugin and plug them in here.

And that is achieved with the help of

what is called as extension points.

So, at each stage, there is an extension point

to which a plugin can be plugged to.



![image](https://github.com/user-attachments/assets/a1ae5d3c-bffd-4b95-b81c-3569cbace841)





Now, all of these are three separate scheduler binaries

that are run with a separate scheduler config file

associated with each of them.

Now, that's one way to deploy multiple schedulers



![image](https://github.com/user-attachments/assets/666da354-c9e7-448e-8924-cb187b85b1ed)




Now the problem here is that

since these are separate processes

there is an additional effort required

to maintain these separate processes



Problem fixed : Now the problem here is that

since these are separate processes

there is an additional effort required

to maintain these separate processes



with the 1.18 release of Kubernetes,

a feature to support multiple profiles



![image](https://github.com/user-attachments/assets/db80dbd5-d633-45d1-b68e-5f05a9688cd6)




