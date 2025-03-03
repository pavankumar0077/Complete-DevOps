Taints & Tolarations

============

By default, pods have no tolerations,
which means unless specified otherwise,
none of the pods can tolerate any taint.

The scheduler tries to place pod A on node one,
but due to the taint it is thrown off
and it goes to node two.

The scheduler then tries to place pod B on node one,
but again, due to the taint,
it is thrown off and is placed on node three,
which happens to be the next free node.
The scheduler then tries to place pod C to the node one.
It is thrown off again and ends up on node two.
And finally the scheduler tries to place pod D on node one.

So remember, taints are set on nodes and tolerations are set on pods.

![image](https://github.com/user-attachments/assets/c0d6ef18-1ab2-491b-b5bc-ffa4958c7cb4)


When the pods are now created or updated
with the new tolerations, they are either
not scheduled on nodes or evicted from the existing nodes  depending on the effect set.

In this example, we have three nodes running some workload.
We do not have any taints or tolerations at this point,
so they're scheduled this way.

We then decided to dedicate node one
for a special application, and as such,
we taint the node with the application name
and add a toleration to the pod
that belongs to the application,
which happens to be pod D in this case.
While tainting the node, we set the taint effect
to no execute, and as such, once the taint
on the node takes effect, it evicts pod C from the node,

which simply means that the pod is killed.
The pod D continues to run on the node
as it has a toleration to the taint blue.
Now, going back to our original scenario
where we have taints and tolerations configured.
Remember taints and tolerations are only meant
to restrict nodes from accepting certain pods.
In this case, node one can only accept pod D,
but it does not guarantee
that pod D will always be placed on node one.
Since there are no taints or restrictions applied
on the other two nodes, pod D may very well be placed
on any of the other two nodes.
So remember, taints and tolerations
does not tell the pod to go to a particular node.
Instead, it tells the node
to only accept pods with certain tolerations.


![image](https://github.com/user-attachments/assets/98e514cf-a200-4de2-9048-128c21facb4c)

schedule any pods on the master node.
Why is that?
When the Kubernetes cluster is first set up,
a taint is set on the master node automatically
that prevents any pods from being scheduled on this node.
