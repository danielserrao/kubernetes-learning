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

To install it follow the steps below:

(On terminal 1)

- cd monitoring-logging
- helm upgrade --install monitoring-demo prometheus-community/kube-prometheus-stack --version 45.5.0 (This version can be changed according to your needs)
- k apply -f grafana-ingress.yaml (This will open the Grafana service to outside the cluster)
- k apply -f prometheus-ingress.yaml (This will open the Prometheus service to outside the cluster)

(On terminal 2)
- Update your /etc/hosts to point 127.0.0.1 to `grafana.demo.com` and `prometheus.demo.com`
- minikube tunnel (This will open a tunnel between the cluster and your WSL)

(On terminal 1)
- curl http://grafana.demo.com (If successful, then you should see the output `<a href="/login">Found</a>`)

If you are using a Linux OS, you should already be able to access http://grafana.demo.com and http://prometheus.demo.com on your browser.

If you are using WSL2 on Windows, then you also need to change the `hosts` file to point 127.0.0.1 to `grafana.demo.com` and `prometheus.demo.com`. Normally this file is located at `C:\Windows\System32\drivers\etc`. Then you should be able to access the apps on a browser on your Windows host.


References:
- https://github.com/prometheus-community/helm-charts/tree/main/charts/kube-prometheus-stack
- https://www.howtogeek.com/27350/beginner-geek-how-to-edit-your-hosts-file/


# Some other notes about kube-prometheus-stack

By default Grafana is already pulling metrics from Prometheus. This can be verified at http://grafana.demo.com/explore.

This is possible because the prometheus endpoint that has the metrics is added as Datasource at http://grafana.demo.com/datasources. If you deploy other apps that provide metrics and/or logs, their endpoints can be added as Datasource in Grafana.

By default, Prometheus Alert Manager and a set of rules are already deployed. You can see these at http://prometheus.demo.com/alerts.


# Loki-distributed

Loki is a horizontally-scalable, highly-available, multi-tenant log aggregation system inspired by Prometheus. It is designed to be very cost effective and easy to operate. It does not index the contents of the logs, but rather a set of labels for each log stream. More information at https://github.com/grafana/loki.

- helm upgrade --install loki-distributed grafana/loki-distributed --values loki-helm-values.yaml --version 0.69.6
- Add http://loki-distributed-query-frontend.default.svc.cluster.local:3100/ as datasource in Grafana.

References:
- https://github.com/grafana/helm-charts/tree/main/charts/loki-distributed
- https://grafana.com/docs/loki/latest/fundamentals/architecture/components/


# FluentD

Fluentd is an open source data collector for unified logging layer. Fluentd allows you to unify data collection and consumption for a better use and understanding of data.

Steps to deploy FluentD using the official helm chart:

(Terminal 1)
- helm repo add fluent https://fluent.github.io/helm-charts
- helm upgrade --install fluentd fluent/fluentd --values fluentd-helm-values.yaml --version 0.3.9

(Terminal 2)
- export POD_NAME=$(kubectl get pods --namespace default -l "app.kubernetes.io/name=fluentd,app.kubernetes.io/instance=fluentd" -o jsonpath="{.items[0].metadata.name}")
- kubectl --namespace default port-forward $POD_NAME 24231:24231
- curl http://127.0.0.1:24231/metrics (Check fluentd metrics)

References:
- https://github.com/fluent/helm-charts/tree/main/charts/fluentd


# Check logs

- To check the logs, go to http://grafana.demo.com/explore and on the top drop menu, select Loki.


# Known bugs

- https://github.com/grafana/helm-charts/issues/1111


# TODO:

- Add loki data source by adding it on the helm values according to https://github.com/prometheus-community/helm-charts/blob/main/charts/kube-prometheus-stack/values.yaml#L876.