# Introduction

Generate a basic pod specification quickly without deploying anything.

This is useful if you want to create a k8s manifest, then make slight changes and use it to deploy.

# Run pod nginx and write its spec into a file called pod.yaml
kubectl run nginx --image=nginx --restart=Never --dry-run=client -o yaml > pod.yaml

# Some of these flags can be useful, depending on the problem
kubectl run nginx --image=nginx --restart=Never --port=80 --labels=key=val --dry-run=client -o yaml > pod.yaml