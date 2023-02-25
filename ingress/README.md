# Introduction

In this example we use Ingress to allow us to access different services using the same address. The service that will be accessed will depend on the path that comes after the address.

To know more about Ingress check the official documentation at https://kubernetes.io/docs/concepts/services-networking/ingress/.


# Steps to start

To allow us to create Ingresses, an Ingress controller needs to be deployed into the cluster first. To enable the NGINX Ingress controller, run the command `minikube addons enable ingress`.

Once the Ingress Controller is deployed, we can create an Ingress resource with the command `k apply -f virtual-host-ingress.yaml`.

The minikube K8s cluster API server IP can be seen with the command `minikube ip`. Then we need to make our local machine to redirect the `blue.example.com` and `green.example.com` to our minikube ip by changing the `/etc/hosts`. The path might be different depending on your OS. 

Then deploy the webserver pods using k8s deployments. 

- `k apply -f webserver-blue.yaml`
- `k apply -f webserver-green.yaml`

To know more about deployments check https://kubernetes.io/docs/concepts/workloads/controllers/deployment/. 

Then deploy the services that will be used by the ingress controller.

- `k apply -f webserver-blue-svc.yaml`
- `k apply -f webserver-green-svc.yaml`

In this case we are using the most simple and use Service type `ClusterIP` which is only accessible from inside the cluster. The exception is if you are using an intermediary like ingress to be able to access it from outside the cluster. The flow in this case is something like `your host` -> `Ingress (located inside the cluster)` -> `ClusterIP Service`. In this case the Ingress exposes the services to outside the cluster.

To know more about K8s service check https://kubernetes.io/docs/concepts/services-networking/service/.


# Delete resources

- `k delete -f virtual-host-ingress.yaml`
- `k delete -f webserver-blue-svc.yaml`
- `k delete -f webserver-green-svc.yaml`
- `k delete -f webserver-blue.yaml`
- `k delete -f webserver-green.yaml`


# Windows SubSystem for Linux (WSL)

When executing this inside a Windows SubSystem with a Linux Distro, you should run `minikube tunnel` inside your linux terminal and this will open a tunnel from your Windows host to you Linux SubSystem for each service and ingresses that you may have.

Then you need to to make your Windows host IP 127.0.0.1 point to blue.example.com and green.example.com by updating the file c:\WINDOWS\system32\drivers\etc\hosts (/etc/hosts equivalent)

Finally, access these addresses on you Windows host browser to make sure that is working.

`minikube tunnel` may lack output with information about the IPs and ports to use for each ingress/service, but this is a bug that can be seen at https://github.com/kubernetes/minikube/issues/12899.


# Limitations

These configurations are only supported on Kubernetes 1.19 or higher. See https://docs.konghq.com/kubernetes-ingress-controller/1.3.x/concepts/ingress-versions for more information.