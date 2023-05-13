# Introduction

This repository is for learning purposes only.

Every folder in this repo will have a `README.MD` file with the steps to create K8s resources.


# Compatibility

These K8s examples were tested with:

- Ubuntu 20.04
- minikube v1.29.0 (tool that creates the local k8s clusters)
- kubectl v1.26.1 (tool that allows you to communicate with the cluster API and deploy resources into the cluster)

Most or all the examples will probably work even with different versions, but its not guaranteed. You may need to do slight changes on the K8s yaml manifests.


# Setting up your local cluster with minikube

First you need to have docker installed and running. Check the offical documentation on how to do it at https://docs.docker.com/desktop/install/linux-install/.

Then you need to install and run minikube according to https://minikube.sigs.k8s.io/docs/start/.

Install kubectl according to https://kubernetes.io/releases/download/#kubectl.

Add kubectl to your PATH permanently. In linux this can be done by adding the following in your `/etc/profile` file:

```
export PATH=$PATH:/usr/local/bin/
alias k=kubectl
complete -o default -F __start_kubectl k
```

This will add kubectl to your PATH and create an alias which allows you to run kubectl by only executing `k`.


# Setting up your local cluster with K3D and Tilt

(TODO)


# List of K8s plugins that I find it useful for better productivity

```
NOTE: When using arkade you might find situations when the tool cannot download the latest version of the CLI or app that you are trying to download
```

- https://github.com/alexellis/arkade#getting-arkade (tool that makes it easy to download a lot of useful other tools, including k8s plugins)
- ark get kubectx (Tool to switch between k8s contexts)
- ark get kubens (Tool to switch between K8s namespaces)
- ark get fzf (Plugin that will allow you to interactively select a context (using kubens) or cluster (using kubectx).

# Tips to troubleshoot issues inside K8s clusters

(TODO)

# TODO

- In each README.MD file inside every folder show the link for the official doc of the resources that are being deployed.
- Setting up your local cluster with K3D and Tilt and update all `README.MDs` to show how to use them.
- Add exercise to learn kustomize in the context of multiple environments. (e.g.: Create Grafana base kustomize files from Official chart and then create patches in different folder fro easier maintenante and upgrades when pulling newer versions from official chart)
- Tips to troubleshoot issues inside K8s clusters.
- Create example for K8s RBAC.
- Update the liveness-probe folder to be named probes and contain one yaml file for each type of probe.
- Create one folder for each K8s service type showing how to deploy them.
- Create README.MD to explain how to deploy K8s infrastructure using maybe https://k3s.io/.


* Create one example for each:
    - Teleport
    - Cert-Manager
    - Goldilocks
    - Cilium (Service Mesh)
    - eBPF
    - NATs
    - SHAR
    - Jaeger

# References and other useful content

- https://kubernetes.io/docs/tutorials/hello-minikube/ (Try kubernetes online without installing anything on your machine)
- https://kubernetes.io/docs/reference/kubectl/cheatsheet/ (List of the most basic and useful K8s commands)
- https://github.com/alexellis/arkade#catalog-of-clis (List of more useful CLIs that you may want to try)