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
