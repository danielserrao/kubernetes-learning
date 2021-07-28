Once the Ingress Controller is deployed, we can create an Ingress resource using the kubectl create command. For example, if we create a virtual-host-ingress.yaml file with the Name-Based Virtual Hosting Ingress rule definition that we saw in the Ingress II section, then we use the following command to create an Ingress resource:

`kubectl apply -f virtual-host-ingress.yaml`


Then we need to make our local machine to redirect the blue.example.com and green.example.com to our minikube ip by changing the /etc/hosts.

Then deploy the webservers and services that will be used by the ingress controller.

`kubectl apply -f webserver-blue-svc.yaml`

`kubectl apply -f webserver-green-svc.yaml`

`kubectl apply -f webserver-blue.yaml`

`kubectl apply -f webserver-green.yaml`