# Introduction

This exercise intends to show how to setup Monitoring and logging of Kubernetes resources using the stack Grafana, Prometheus, Loki and FluentD.

# Requirements

- Have all tools mentioned on the README.MD on the root of this repo installed (minikube, etc)
- Install Helm 3.11.1 or later according to https://helm.sh/docs/intro/install/

# Initial steps

- sudo service docker start
- minikube start
- helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
- helm repo update

# Install kube-prometheus-stack

kube-prometheus-stack is a collection of Kubernetes manifests, Grafana dashboards, and Prometheus rules combined with documentation and scripts to provide easy to operate end-to-end Kubernetes cluster monitoring with Prometheus using the Prometheus Operator.

To know more about it, go to https://github.com/prometheus-community/helm-charts/tree/main/charts/kube-prometheus-stack.

To install it follow the steps below:

(On terminal 1)

- cd monitoring-logging
- helm upgrade monitoring-demo prometheus-community/kube-prometheus-stack --version 45.5.0 (This version can be changed according to your needs)
- k apply -f grafana-ingress.yaml (This will open the Grafana service to outside the cluster)
- k apply -f prometheus-ingress.yaml (This will open the Prometheus service to outside the cluster)

(On terminal 2)
- Update your /etc/hosts to point 127.0.0.1 to grafana.demo.com and prometheus.demo.com
- minikube tunnel (This will open a tunnel between the cluster and your WSL)

(On terminal 1)
- curl http://grafana.demo.com (If successful, then you should see the output `<a href="/login">Found</a>`)

If you are using a Linux OS, you should already be able to access http://grafana.demo.com and http://prometheus.demo.com on your browser.

If you are using WSL2 on Windows, then you also need to change the `hosts` file to point 127.0.0.1 to http://grafana.demo.com and http://prometheus.demo.com. Normally this file is located at `C:\Windows\System32\drivers\etc`. Then you should be able to access the apps on a browser on your Windows host.


References:
- https://github.com/prometheus-community/helm-charts/tree/main/charts/kube-prometheus-stack
- https://www.howtogeek.com/27350/beginner-geek-how-to-edit-your-hosts-file/


# Some other notes about kube-prometheus-stack

By default Grafana is already pulling metrics from Prometheus. This can be verified at http://grafana.demo.com/explore.

This is possible because the prometheus endpoint that has the metrics is added as Datasource at http://grafana.demo.com/datasources. If you deploy other apps that provide metrics and/or logs, their endpoints can be added as Datasource in Grafana.

By default, Prometheus Alert Manager and a set of rules are already deployed. You can see these at http://prometheus.demo.com/alerts.
