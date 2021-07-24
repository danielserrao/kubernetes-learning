## Introduction

Create the nginx webserver application

`kubectl create -f webserver.yaml`

For a NodePort ServiceType, Kubernetes opens up a static port on all the worker nodes. If we connect to that port from any node, we are proxied to the ClusterIP of the Service. Next, let's use the NodePort ServiceType while creating a Service.

`kubectl create -f webserver-svc.yaml`

Get Minikube IP (K8s Cluster IP)

`minikube ip`


Get ClusterIP and Node port

`kubectl get services`

We have reserved a static port <node_port> on the node. If we connect to the node on that port, our requests will be proxied to the ClusterIP on port 80.

You can access the application on <minikube_ip>:<node_port> or by executing the following command

`minikube service web-service`

Sometimes there is a bug where the page is not found, for a short time. If that happens please wait a couple of minutes and try again using both approaches.

