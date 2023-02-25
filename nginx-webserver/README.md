## Introduction

This directory intends to help on learning how to deploy and configure a simple nginx webserver.

In this example we show how to deploy simple webserver pods using a K8s Deployment and also how to make these pods get environment variables from a ConfigMap.


# Create ConfigMap

ConfigMaps allow us to decouple the configuration details from the container image. Using ConfigMaps, we pass configuration data as key-value pairs, which are consumed by Pods or any other system components and controllers, in the form of environment variables, sets of commands and arguments, or volumes. We can create ConfigMaps from literal values, from configuration files, from one or more files or directories.

We can then create the ConfigMap with the command `k create -f customer1-configmap.yaml`.

To know more about ConfigMaps check the official documentation at https://kubernetes.io/docs/concepts/configuration/configmap/.


# Create the nginx webserver application

To  deploy the pods you can use the deployment yaml file executing the command `k apply -f webserver.yaml`.

To know more about deployments check https://kubernetes.io/docs/concepts/workloads/controllers/deployment/. 

For a NodePort ServiceType, Kubernetes opens up a static port on all the worker nodes. If we connect to that port from any node, we are proxied to the ClusterIP of the Service. Next, let's use the NodePort ServiceType while creating a Service with the command `k apply -f webserver-svc.yaml`

To know more about K8s service check https://kubernetes.io/docs/concepts/services-networking/service/.

To update the Deployment or ConfigMap you can update the yaml files and execute the same commands above.


# Get Minikube IP (K8s Cluster IP)

`minikube ip`


# Get ClusterIP and Node port

`k get services`

We have reserved a static port <node_port> on the node. If we connect to the node on that port, our requests will be proxied to the ClusterIP on port 80.

You can access the application on <minikube_ip>:<node_port> or by executing the following command

`minikube service web-service`

Sometimes there is a bug where the page is not found, for a short time. If that happens please wait a couple of minutes and try again using both approaches.


# See the environment variables of configmap

kubectl exec -i -t <pod-name> -- env

If you update the key values on the configmap, update it, delete the pod and check again the environment variables, you will see the updated values.

This means that the environment variables are not update in alive pods and that only the new pods will get the new configuration.


# Delete resources

- `k delete -f webserver.yaml && k delete -f webserver-svc.yaml && k delete -f customer1-configmap.yaml`


# The use of the Kubernetes command field

The command field corresponds to entrypoint in some container runtimes.

In the case of Docker, the Kubernetes command field will override the entrypoint of the image.

The entrypoint command is executed by the main process which should always be running otherwise the Pod will be considered Completed and won't run any container.

One way of fixing this when using nginx, is to run the command `nginx -g 'daemon off;'` at the end which will make the main process to keep running nginx and keep the container running.