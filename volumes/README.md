# Introduction

Persistent Volumes (PVs) and Persistent Volume Claims (PVCs) decouple pods from storage technology. PVs are created by cluster administrators or dynamically based on Storage Classes.

As opposed to the volumes we created earlier, defined at the pod level, PVs that have their own life cycle, independent from any pod.

After creating PVs, users can create Persistent Volume Claims to get the storage they need, without needing to care about the actual infrastructure that is backing their storage.

To know more about K8s volumes check https://kubernetes.io/docs/concepts/storage/volumes/.


# Create the PV and PVC

- `k apply -f pv.yaml`
- `k apply -f pvc.yaml`

# Create a pod which will use the PV and PVC create above

- `k apply -f with-mounted-volume.yaml`
- `k get pods -w`

# Create a file at the mounted location

- `k exec -it with-mounted-volume -- sh -c "echo 'Inside the pod' > /tmp/foo/newfile"`

# Try to read the file

- `k exec -it with-mounted-volume -- cat /tmp/foo/newfile`

# Delete the pod

- `k delete pods with-mounted-volume`

# Create a new pod

- `k apply -f with-mounted-volume.yaml`

# Explore the content of `/tmp/foo`  to see if it persisted

- `k exec -it with-mounted-volume -- cat /tmp/foo/newfile`


# Check in which node the pod is located

- `k describe pod with-mounted-volume`
- On the output see the value of `Node`


# Access minikube node where persistent volume is located

If it is the default node minikube, you just need to

`minikube ssh`

otherwise you need to use the flag --node

`minikube ssh --node=m02`

Then check the file with

`ls -la /etc/foo`

The files is located at `/etc/foo` because the it is the `HostPath` defined in the `pv.yaml`.


# Delete resources

- `k delete -f with-mounted-volume.yaml && k delete -f pvc.yaml && k delete -f pv.yaml`