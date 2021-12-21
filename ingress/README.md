## Steps to start

To enable the NGINX Ingress controller, run the following command:

`minikube addons enable ingress`

Once the Ingress Controller is deployed, we can create an Ingress resource using the kubectl create command. For example, if we create a virtual-host-ingress.yaml file with the Name-Based Virtual Hosting Ingress rule definition that we saw in the Ingress II section, then we use the following command to create an Ingress resource:

`kubectl apply -f virtual-host-ingress.yaml`


Then we need to make our local machine to redirect the blue.example.com and green.example.com to our minikube ip by changing the /etc/hosts.

Then deploy the webservers and services that will be used by the ingress controller.

`kubectl apply -f webserver-blue-svc.yaml`

`kubectl apply -f webserver-green-svc.yaml`

`kubectl apply -f webserver-blue.yaml`

`kubectl apply -f webserver-green.yaml`


## Windows SubSystem for Linux (WSL)

When executing this inside a Windows SubSystem with a Linux Distro, you should run `minikube tunnel` inside your linux terminal and this will open a tunnel from your Windows host to you Linux SubSystem for each service and ingresses that you may have.

Then you need to to make your Windows host IP 127.0.0.1 point to blue.example.com and green.example.com by updating the file c:\WINDOWS\system32\drivers\etc\hosts (/etc/hosts equivalent)

Finally, access these addresses on you Windows host browser to make sure that is working.

`minikube tunnel` may lack output with information about the IPs and ports to use for each ingress/service, but this is a bug that can be seen at https://github.com/kubernetes/minikube/issues/12899.


## Limitations

These configurations are only supporte on Kubernetes 1.19 or higher. See https://docs.konghq.com/kubernetes-ingress-controller/1.3.x/concepts/ingress-versions for more information.