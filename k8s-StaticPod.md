But how do you provide the Pod definition file
to the kubelet without a kube-apiserver?
You can configure the kubelet
to read the Pod definition files
from a directory on the server
designated to store information about Pods.
Place the Pod definition files in this directory.
 
If you make a change to any of the file
within this directory, the kubelet recreates the Pod
for those changes to take effect.
If you remove a file from this directory,
the Pod is deleted automatically.
 
NOTE: WE CAN ONLY CREATE A POD LIKE THIS, NO RS, DS AND ETC
 
The option is named pod-manifest-path,
and here it is set to etc/Kubernetes/manifests folder.

![image](https://github.com/user-attachments/assets/d9ceb55d-8d2c-4bb7-9b3f-70ee0593092d)

Why Use Static Pods?
Critical System Components: Static Pods are often used for running essential components of the Kubernetes control plane, such as the API server, controller manager, and scheduler. These components need to be highly available and directly managed by the kubelet on each node to ensure the cluster's stability and reliability.
Node-Specific Applications: Sometimes, you might have applications or services that need to run on specific nodes due to hardware dependencies or other requirements. Static Pods allow you to ensure these applications are always running on the designated nodes.
Bootstrapping Clusters: When initially setting up a Kubernetes cluster, Static Pods can be used to bootstrap the cluster. For example, kubeadm uses Static Pods to deploy the initial control plane components before the rest of the cluster is fully operational.
Real-Time Example
Imagine you are setting up a Kubernetes cluster and need to ensure that the API server is always running on the master node. The API server is crucial for the cluster's operation, and any downtime can affect the entire cluster. By using a Static Pod for the API server, you ensure that the kubelet on the master node directly manages it. If the API server crashes, the kubelet will automatically restart it, ensuring high availability.
The first is through the Pod definition files
from the static Pods folder, as we just saw.
The second is through an HTTP API endpoint,
 
If you run the kubectl get pods command on the master Node,
the static Pods will be listed as any other Pod.
Well, how is that happening?
When the kubelet creates a static Pod,
if it is a part of a cluster,
it also creates a mirror object in the kube-apiserver.
What you see from the kube-apiserver
is just a read-only mirror of the Pod.
You can view details about the Pod,
 
So then why would you want to use static Pods?
Since static Pods are not dependent
on the Kubernetes control plane, you can use static Pods
to deploy the control plane components itself
as Pods on a Node.
Well, start by installing kubelet on all the master Nodes,
then create Pod definition files that uses docker images
of the various control plane components
such as the API server, controller, ETCD, et cetera.
Place the definition files
in the designated manifest folder,
and the kubelet takes care of deploying
the control plane components themselves
as Pods on the cluster.
This way, you don't have to download the binaries,
configure services, or worry about the services crashing.
If any of these services were to crash,
since it's a static Pod,
it'll automatically be restarted by the kubelet.
Nice and simple.

![image](https://github.com/user-attachments/assets/21c23e3c-a263-4e54-b6fa-4ec7581268f8)

