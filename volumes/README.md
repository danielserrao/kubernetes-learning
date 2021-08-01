# Introduction

Persistent Volumes (PVs) and Persistent Volume Claims (PVCs) decouple pods from storage technology. PVs are created by cluster administrators or dynamically based on Storage Classes.

As opposed to the volumes we created earlier, defined at the pod level, PVs that have their own life cycle, independent from any pod.

After creating PVs, users can create Persistent Volume Claims to get the storage they need, without needing to care about the actual infrastructure that is backing their storage.


# Create a pod

`kubectl apply -f with-mounted-volume.yaml`

# Create a file at the mounted location

`kubectl exec -it with-mounted-volume -- sh -c "echo 'Inside the pod' > /tmp/foo/newfile"`

# Try to read the file

`kubectl exec -it with-mounted-volume -- cat /tmp/foo/newfile`

# Delete the pod

`kubectl delete pods with-mounted-volume`

# Create a new pod

`kubectl apply -f with-mounted-volume.yaml`

# Explore the content of `/tmp/foo`  to see if it persisted

`kubectl exec -it with-mounted-volume -- cat /tmp/foo/newfile`


# Check in which node the pod is located

`kubectl describe pod with-mounted-volume`

# Access minikube node where persistent volume is located

If it is the default node minikube, you just need to

`minikube ssh`

otherwise you need to use the flag --node

`minikube ssh --node=m02`

Then check the file with

`ls -la /etc/foo`