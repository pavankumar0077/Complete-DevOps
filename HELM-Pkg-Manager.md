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

