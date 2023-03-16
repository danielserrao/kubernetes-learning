## Introduction

cert-manager adds certificates and certificate issuers as resource types in Kubernetes clusters, and simplifies the process of obtaining, renewing and using those certificates.

It can issue certificates from a variety of supported sources, including Let's Encrypt, HashiCorp Vault, and Venafi as well as private PKI.

Know more about cert-manager at https://cert-manager.io/docs/.


## Steps

- `sudo service docker start`
- `minikube start`
- `helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx`
- `helm repo update`
- `minikube addons enable ingress`

```
helm install \
  cert-manager jetstack/cert-manager \
  --namespace cert-manager \
  --create-namespace \
  --version v1.11.0 \
  --set installCRDs=true
```



## TODO

- Do local example with self signed certificates by following https://cert-manager.io/docs/configuration/selfsigned/
- Explain every cert-manager component.
- Explain differences with the no self-signed certificates.
- Provide link for non self-signed example like https://cert-manager.io/docs/tutorials/acme/nginx-ingress/.
- Explain TLS encryption flow.