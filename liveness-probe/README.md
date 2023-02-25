# Introduction

In this example we create a pod with a liveness probe that will try to read the file `/tmp/healthy`. The pod will run an initial command that will first create this file and then delete it after 30 seconds. When this happen, the liveness probe with restart the pod because the test failed.

To know more about probes check https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/.

# Steps

Create container with liveness probe with the command `k create -f probe.yaml`

See the number of restarts increasing every minute +- using one of the commands below

- `k get pods -w`
- `k describe pod liveness-exec`


# Delete resources

- `k delete -f probe.yaml`
