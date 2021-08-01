# Introduction

Generate a basic pod specification quickly.

# Run pod nginx and write its spec into a file called pod.yaml
kubectl run nginx --image=nginx --restart=Never --dry-run=client -o yaml > pod.yaml

# Some of these flags can be useful, depending on the problem
kubectl run nginx --image=nginx --restart=Never  --port=80 --labels=key=val --dry-run=client -o yaml > pod.yaml