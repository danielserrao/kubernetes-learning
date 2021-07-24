## Introduction

This directory intends to help on learning how to deploy and configure a simple nginx webserver.

# Create ConfigMap

ConfigMaps allow us to decouple the configuration details from the container image. Using ConfigMaps, we pass configuration data as key-value pairs, which are consumed by Pods or any other system components and controllers, in the form of environment variables, sets of commands and arguments, or volumes. We can create ConfigMaps from literal values, from configuration files, from one or more files or directories.

We can then create the ConfigMap with the following command:

`kubectl create -f customer1-configmap.yaml`


# Create the nginx webserver application

`kubectl apply -f webserver.yaml`

For a NodePort ServiceType, Kubernetes opens up a static port on all the worker nodes. If we connect to that port from any node, we are proxied to the ClusterIP of the Service. Next, let's use the NodePort ServiceType while creating a Service.

`kubectl apply -f webserver-svc.yaml`

To update you can execute the same commands above.

# Get Minikube IP (K8s Cluster IP)

`minikube ip`


# Get ClusterIP and Node port

`kubectl get services`

We have reserved a static port <node_port> on the node. If we connect to the node on that port, our requests will be proxied to the ClusterIP on port 80.

You can access the application on <minikube_ip>:<node_port> or by executing the following command

`minikube service web-service`

Sometimes there is a bug where the page is not found, for a short time. If that happens please wait a couple of minutes and try again using both approaches.


# See the environment variables of configmap

kubectl exec -i -t <pod-name> -- env

If you update the key values on the configmap, update it, delete the pod and check again the environment variables, you will see the updated values.

This means that the environment variables are not update in alive pods and that only the new pods will get the new configuration.


# The use of the Kubernetes command field

The command field corresponds to entrypoint in some container runtimes.

In the case of Docker, the Kubernetes command field will override the entrypoint of the image.

The entrypoint command is executed by the main process which should always be running otherwise the Pod will be considered Completed and won't run any container.

One way of fixing this when using nginx, is to run the command `nginx -g 'daemon off;'` at the end which will make the main process to keep running nginx and keep the container running.