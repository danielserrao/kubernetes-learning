# Introduction

This is another example of using config maps.


# Create the configMap

`kubectl apply -f web-config.yaml`


# Create the pod running the nginx app

`kubectl apply -f app-config.yaml`


# Check if the index.html was updated accordingly to configMap data key string

`kubectl exec app-config -- /bin/sh -c 'cat /usr/share/nginx/html/index.html'`


# The use of the Kubernetes command field

The command field corresponds to entrypoint in some container runtimes.

In the case of Docker, the Kubernetes command field will override the entrypoint of the image.

The entrypoint command is executed by the main process which should always be running otherwise the Pod will be considered Completed and won't run any container.

One way of fixing this when using nginx, is to run the command `nginx -g 'daemon off;'` at the end which will make the main process to keep running nginx and keep the container running.