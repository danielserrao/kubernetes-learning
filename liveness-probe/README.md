
Create container with liveness probe

`kubectl create -f probe.yaml`

See the number of restarts increasing from time to time using one of the commands below

`kubectl get pods`

`kubectl describe pod liveness-exec`