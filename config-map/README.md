# Introduction

On this example we show that we can use ConfigMaps to pass values into pods environment variables and use these to update the content of files.

To know more about ConfigMaps check the official documentation at https://kubernetes.io/docs/concepts/configuration/configmap/.

# Check the current list of configmaps

- `k get cm`

# Create the configMap and confirm that it was created

- `k apply -f configmap.yaml`
- `k get cm`

# Create the pod running the nginx app

- `k apply -f pod.yaml`
- `k get pods`


# Check the current index.html accordingly to configMap data key string

- `k exec pod -- /bin/sh -c 'cat /usr/share/nginx/html/index.html'`
- You should see the message `Welcome to MY-NGINX!`.


# Update the config map

- Change the file `configmap.yaml` message to `Welcome to MY-NGINX! Version 2` and save the file.
- `k apply -f configmap.yaml`
- `k delete -f pod.yaml` (You need to redeploy the pod to make it fetch the latest config map version)
- `k apply -f pod.yaml`
- `k exec pod -- /bin/sh -c 'cat /usr/share/nginx/html/index.html'`
- You should see the message `Welcome to MY-NGINX! Version 2` which confirms that you successfully update the environment variable inside the pod!


# Delete resources

- `k delete -f pod.yaml`
- `k delete -f configmap.yaml`


# The use of the Kubernetes CMD field

The `command` field in the `pod.yaml` file corresponds to entrypoint in some container runtimes such as docker.

In the case of Docker, the Kubernetes CMD field will override the entrypoint of the image.

The entrypoint CMD is executed by the main process which should always be running otherwise the Pod will be considered Completed and won't run any container.

One way of fixing this when using nginx, is to run the command `nginx -g 'daemon off;'` at the end which will make the main process to keep running nginx and keep the container running.