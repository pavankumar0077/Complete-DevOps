# KUBERNETES PACKAGE MANAGER

Ubuntu    --    apt
Kuberntes    --    Helm

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/4ded390e-d8ad-49d3-a877-6994ce8a2335)

## Three Big Concepts
- A Chart is a Helm package. It contains all of the resource definitions necessary to run an application, tool, or service inside of a
Kubernetes cluster. Think of it like the Kubernetes equivalent of a Homebrew formula, an Apt dpkg, or a Yum RPM file.

- A Repository is the place where charts can be collected and shared. It's like Perl's CPAN archive or the Fedora Package
Database, but for Kubernetes packages.

- A Release is an instance of a chart running in a Kubernetes cluster. One chart can often be installed many times into the same
cluster. And each time it is installed, a new release is created. Consider a MySQL chart. If you want two databases running in
your cluster, you can install that chart twice. Each one will have its own release, which will in turn have its own release name.

- With these concepts in mind, we can now explain Helm like this:
Helm installs charts into Kubernetes, creating a new release for each installation. And to find new charts, you can search Helm
chart repositories.

## HELM Install the resources in specific order 
![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/5a330153-04c8-4fa4-82f7-563863e6c4c2)

![image](https://github.com/pavankumar0077/Complete-DevOps/assets/40380941/d4467649-5df5-466d-8211-23447c2b1f66)

What is Helm CHart ?
- A Helm chart is a package that contains all the necessary resources to deploy an application to a Kubernetes cluster. This includes YAML configuration files for deployments, services, secrets, and config maps that define the desired state of your application.

REF LINKS
--
1) https://helm.sh/docs/helm/helm/
2) https://github.com/Azure-Samples/helm-charts/tree/master/chart-source
3) https://helm.sh/docs/topics/v2_v3_migration/
4) https://helm.sh/docs/helm/helm_create/
5) https://github.com/bregman-arie/devops-exercises/tree/master/topics/kubernetes#helm

Add Repo for templates/charts
--
```
helm repo add bitnami https://charts.bitnami.com/bitnami

helm search repo bitnami

helm install my-release bitnami/<chart>
```
REF LINK : https://charts.bitnami.com/

### BITNAMI is like DockerHub for kubernetes

To Pull image from Bitnami 
--
```
helm pull bitnami/nginx --untar=true
```
Here we are pulling nginx template/chart

To install nginx using helm charts
--
```
helm install my-nginx bitnami/nginx
```
NOTE : We can edit or modify the templates as per our requirement

output :
```
idrbt@idrbt:~/kubernetes-sample$ helm install my-nginx bitnami/nginx
NAME: my-nginx
LAST DEPLOYED: Wed Oct 18 21:46:18 2023
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
CHART NAME: nginx
CHART VERSION: 15.3.4
APP VERSION: 1.25.2

** Please be patient while the chart is being deployed **
NGINX can be accessed through the following DNS name from within your cluster:

    my-nginx.default.svc.cluster.local (port 80)

To access NGINX from outside the cluster, follow the steps below:

1. Get the NGINX URL by running these commands:

  NOTE: It may take a few minutes for the LoadBalancer IP to be available.
        Watch the status with: 'kubectl get svc --namespace default -w my-nginx'

    export SERVICE_PORT=$(kubectl get --namespace default -o jsonpath="{.spec.ports[0].port}" services my-nginx)
    export SERVICE_IP=$(kubectl get svc --namespace default my-nginx -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
    echo "http://${SERVICE_IP}:${SERVICE_PORT}"
```

To update helm charts with values
--
```
helm upgrade my-nginx ./nginx
```
NOTE : we have chnaged cluster ip to nodeport type ip
```
 echo "http://${NODE_IP}:${NODE_PORT}"
```
List Helm Releases:
--
```
helm list
```

Uninstall the Helm Release:
--
```
helm uninstall my-nginx
```

```
idrbt@idrbt:~/kubernetes-sample$ helm list
NAME    	NAMESPACE	REVISION	UPDATED                                	STATUS  	CHART       	APP VERSION
my-nginx	default  	2       	2023-10-18 22:29:48.153037458 +0530 IST	deployed	nginx-15.3.4	1.25.2     
idrbt@idrbt:~/kubernetes-sample$ helm uninstall my-nginx
release "my-nginx" uninstalled
```
