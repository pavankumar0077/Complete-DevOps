![image](https://github.com/user-attachments/assets/b40092cd-a375-4339-9d0c-73b2df0d1fda)

We have three nodes and three pods each in three colors,
blue, red, and green.
The ultimate aim is to place the blue pod in the blue node,
the red pod in the red node, and likewise for green.
We are sharing the same Kubernetes cluster with other teams.
So there are other pods in the cluster
as well as other nodes.
We do not want any other pod to be placed on our node.
Neither do we want our pods to be placed on their nodes.
Let us first try to solve this problem
using taints and tolerations.
We apply a taint to the nodes
marking them with their colors, blue, red, and green,
and we then set a toleration on the pods
to tolerate the respective colors.
When the pods are now created,
the nodes ensure they only accept the pods
with the right toleration.
So the green pod ends up on the green node,
and the blue pod ends up on the blue node.
However, taints and tolerations does not guarantee
that the pods will only prefer these nodes.
So the red node ends up on one of the other nodes
that do not have a taint or toleration set.
This is not desired.

![image](https://github.com/user-attachments/assets/958b5cb0-b2ba-4cc4-a5a3-4e392932f458)


With node affinity, we first label the nodes
with their respective colors, blue, red, and green.
We then set node selectors on the pods
to tie the pods to the nodes.
As such, the pods end up on the right nodes.
However, that does not guarantee
that other pods are not placed on these nodes.
In this case, there is a chance
that one of the other pods may end up on our nodes.
 
![image](https://github.com/user-attachments/assets/92baecc2-1ac4-424e-a7ba-d2b8db60a661)

As such, a combination of taints, and tolerations,
and node affinity rules can be used together
to completely dedicate nodes for specific pods.
We first use taints and tolerations to prevent other pods
from being placed on our nodes,
and then we use node affinity to prevent our pods
from being placed on their nodes.
