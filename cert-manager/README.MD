## Introduction

cert-manager adds certificates and certificate issuers as resource types in Kubernetes clusters, and simplifies the process of obtaining, renewing and using those certificates.

It can issue certificates from a variety of supported sources, including Let's Encrypt, HashiCorp Vault, and Venafi as well as private PKI.

Know more about cert-manager at https://cert-manager.io/docs/.


## Steps to create certificate and use it to implement TLS on ingress

First lets prepare our cluster with the following steps:

- `sudo service docker start`

- `minikube start`

- If you get an error similar to `Usage of CRI endpoints without URL scheme is deprecated and can cause kubelet errors`, please try `minikube delete --all --purge` and `minikube start --driver=docker --force --extra-config=kubelet.cgroup-driver=systemd --cni calico --container-runtime=containerd --registry-mirror=https://registry.docker-cn.com` (https://github.com/kubernetes/minikube/issues/16073) 

- `helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx`
- `helm repo update`
- `minikube addons enable ingress`


Deploy the cert-manager with the official helm chart with the below command:

```
helm install \
cert-manager jetstack/cert-manager \
--namespace cert-manager \
--create-namespace \
--version v1.11.0 \
--set installCRDs=true
```


Deploy a namespace with the name sandbox and deploy a simple nginx app called blue with the commands:

- `k apply -f sandbox-namespace.yaml`
- `k apply -f webserver-blue.yaml` (Deploy blue app pods)
- `k apply -f webserver-blue-svc.yaml` (Deploy service load balance between pods)
- `k apply -f test-ingress.yaml` (Deploy ingress that allows us to access the blue service from outside the cluster)
- On another terminal execute `minikube tunnel` (This allows connectivity between your host and the cluster)

- The minikube K8s cluster API server IP can be seen with the command `minikube ip`. Then we need to make our local machine to redirect the `blue.example.com` to our minikube ip by changing the `/etc/hosts`. The path might be different depending on your OS. 

Now when executing `curl http://blue.example.com` you should get:

```
<html>
<head><title>308 Permanent Redirect</title></head>
<body>
<center><h1>308 Permanent Redirect</h1></center>
<hr><center>nginx</center>
</body>
</html>
```

This means that it is working and you should be able to see the app on your browser at https://blue.example.com, but with an insecure connection without TLS. If you are using WSL, then you should also follow the section `Windows SubSystem for Linux (WSL)` below if you didn't before.

If you execute the command `curl https://blue.example.com`, you should see:

```
curl: (60) SSL certificate problem: unable to get local issuer certificate
More details here: https://curl.haxx.se/docs/sslcerts.html

curl failed to verify the legitimacy of the server and therefore could not
establish a secure connection to it. To learn more about this situation and
how to fix it, please visit the web page mentioned above.
```

This also indicates that TLS / HTTPS is not working.

Setup TLS with the following commands:

- `k apply -f selfsigned-cluster-issuer.yaml` (Create a the `selfsigned-cluster-issuer`)
- `k get clusterissuers` (See the new cluster issuer that was created)
- `k apply -f test-ca-certificate.yaml` (This will create the the `test-ca` issued by the `selfsigned-cluster-issuer` and also a secret called `test-ca` that will contain the CA/root certificate in PEM format)
- `k get certificates -n sandbox` (See the new certificate that was created)
- `k apply -f test-ca-cluster-issuer.yaml` ( Create a new Cluster CA issuer `test-ca-cluster-issuer` that will use the secret `test-ca` which has the previous CA/root certificate)
- `k apply -f test-ingress.yaml` (Create an ingress that will set TLS with a certificate provided by `test-ca-cluster-issuer`)

If you now try to `curl https://blue.example.com`, you will still get the same result because the CA/root certificate need to be added in your host local trust store. 

On Ubuntu 20.10, this can be done with the commands:

- `sudo apt-get install -y ca-certificates`
- `k get secret -n cert-manager test-ca -o json | jq -r  '.data["ca.crt"]' | base64 -d > local-ca.crt`
- `sudo cp local-ca.crt /usr/local/share/ca-certificates`

If you are using Windows, then you can follow the steps at https://www.itechtics.com/update-root-certificates/.

Now you should be able to see the blue app page working with the "secure" connection on your browser. If it still is marked as unsecure, it might be cache related and you should try with an anonymous browser. Alternatevely you can execute `curl https://blue.example.com` and get the following result:

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body style="background-color:blue;">
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```

If you got this result, congrats! You just setup TLS on an app running inside kubernetes!


## How TLS/SSL encryption works

1) Client requests public key to server.
2) Server gives public key + digital certificate to client. The certificate contains info like the domain and IP of the server.
3) Client sends a request to the CA (specified in the client trust store locally) with the certificate to confirm if the server is really who it says it is.
4) Client creates a session key and encrypts it with the public key (asymmetric encryption). 
5) Client sends the encrypted session key to the server which can only be decrypted by the server.
6) Then the session key is used by both to encrypt and decrypt data that is sent between them (symmetric encryption).


## What is TLS?

TLS which is the former SSL.

TLS = Transport Layer Security

SSL = Secure Socket Layer

TLS is a cryptographic protocol that provides end-to-end security of data sent between applications over the Internet.
	
Private key is in a server and is kept private.

Public key is available for everyone.

TLS uses asymmetric cryptography (public and private keys) to generate a temporary session key, then this key is used to encrypt and decrypt the data shared between the server and client.

This session key is temporary and a new session key will be generated using asymmetric cryptography making it difficult for an hacker to decrypt any data considering that it has limited time to find out the session key that is being used and decrypt the data and even if it founds it, it will lose the capacity to decrypt the data as soon as the session key is disabled.

The reason why symmetric cryptography is used during the session is because asymmetric cryptography has an impact on how fast data is shared.



# Windows SubSystem for Linux (WSL)

When executing this inside a Windows SubSystem with a Linux Distro, you should run `minikube tunnel` inside your linux terminal and this will open a tunnel from your Windows host to you Linux SubSystem for each service and ingresses that you may have.

Then you need to to make your Windows host IP 127.0.0.1 point to blue.example.com and green.example.com by updating the file c:\WINDOWS\system32\drivers\etc\hosts (/etc/hosts equivalent)

Finally, access these addresses on you Windows host browser to make sure that is working.

`minikube tunnel` may lack output with information about the IPs and ports to use for each ingress/service, but this is a bug that can be seen at https://github.com/kubernetes/minikube/issues/12899.


## Cert-manager terminologies

#### Issuer

In cert-manager, an issuer is a Kubernetes resource that defines how certificates should be issued.

    - CA issuer: This type of issuer is used to issue certificates signed by a Certificate Authority (CA).
    - Self-signed issuer: This type of issuer is used to issue self-signed certificates, where the certificate is signed by the same entity that created it.
    - ACME issuer: This type of issuer is used to issue certificates using the Automated Certificate Management Environment (ACME) protocol, which is used by Let's Encrypt and other Certificate Authorities.
    - Vault issuer: This type of issuer is used to issue certificates using HashiCorp Vault, a tool for managing secrets and cryptographic keys.


#### ClusterIssuer

The main difference between an Issuer and a ClusterIssuer is their scope.

    - An Issuer is scoped to a single Kubernetes namespace, which means that certificates issued by an Issuer can only be used within that namespace.
    - A ClusterIssuer is scoped to the entire Kubernetes cluster, which means that certificates issued by a ClusterIssuer can be used by any workload running in any namespace within the cluster.

## References

- https://cert-manager.io/docs/installation/helm/
- https://cert-manager.io/docs/configuration/selfsigned/
- https://gist.github.com/jakexks/c1de8238cbee247333f8c274dc0d6f0f
- https://github.com/cert-manager/cert-manager/issues/650
- https://ubuntu.com/server/docs/security-trust-store

## TODO

- Explain differences with the non self-signed certificates.
- Provide link for non self-signed example like https://cert-manager.io/docs/tutorials/acme/nginx-ingress/.