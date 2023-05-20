## Prerequistes

* AWS account
* Kubernetes >= v1.14.0
* Helm >= v3.2.0
* Own a domain

## Introduction

(TODO)


## STEPS

* `sudo service docker start`

* `minikube start`

* ./install-eksctl-unix.sh (Install eksctl)
* export KUBECONFIG=~/.kube/eks-test1-config

```
NOTE: Fargate version of EKS doesn't seem to be compatible with cert-manager, so we will deploy without Fargate.

According to https://repost.aws/knowledge-center/eks-resolve-pending-fargate-pods the pods need to be set to be scheduled by fargate-scheduler, but the cert-manager helm-chart uses `default-scheduler` and it doesn't have a dynamic way to change this unless you change the official helm-chart or override it using kustomize.

Considering that this type of issue might happen in many other applications, I assume using EKS without Fargate is the best option to make it easier and more maintainable.
```

* `eksctl create cluster --name eks-test1 --region eu-west-2 --fargate`

* `helm repo add teleport https://charts.releases.teleport.dev`
* `helm repo update`

* Create a bucket to be used by teleport if you don't have one.

* Create a Route 53 hosted zone to be used by teleport if you don't have one.

* If your domain is registered outside of AWS Route53, then you should update your DNS records in your domain provider to use the nameservers in your Route53 hosted zone. After this is done, it might take a couple of hours for the DNS to propagate. You can test when it is propagated with the command `dig ns <your-domain>`.

* Add the policies in `iam-policies` folder on the new EKS Role created. You can find in IAM roles and it should be similar to eksctl-eks-test1-nodegroup-ng-b85-NodeInstanceRole-*.

* You should replace some values on the files in the `iam-policies` foldee, `aws-issuer.yaml` and `aws-values.yaml`. These values are `832044644018` to be your AWS account ID, the `eu-west-2` your region, `danielserrao-test` your teleport S3 bucket, `Z10178624UNKYF8SFVEM` your Hosted Zone ID and `danielserrao.life` your domain.

* Also replace the MYZONE_DNS, MYDNS, MY_CLUSTER_REGION in `set-dns-records.sh` accordingly.

* helm repo add jetstack https://charts.jetstack.io

* helm repo update

helm install cert-manager jetstack/cert-manager \
--create-namespace \
--namespace cert-manager \
--set installCRDs=true \
--set extraArgs="{--issuer-ambient-credentials}"

* `kubectl --namespace teleport create -f aws-issuer.yaml`

* `k get issuers -n teleport (Check issuer)`

helm install teleport teleport/teleport-cluster \
--create-namespace \
--namespace teleport \
-f aws-values.yaml

* ./set-dns-records.sh

* Instal `tsh` CLI according to https://goteleport.com/docs/get-started/#step-46-log-in-using-tsh

* tsh login --proxy=teleport.danielserrao.life:443 --auth=local --user=test teleport.danielserrao.life

* export KUBECONFIG=${HOME?}/teleport-kubeconfig.yaml

* tsh kube login teleport.danielserrao.life

* kubectl get pods


## DELETE CLUSTER

* At the end of the exercise, you should delete the cluster with the command `eksctl delete cluster --name eks-test1 --region eu-west-2` to avoid unnecessary costs!

## TODO

- After the steps above, the user `test` still cannot get resources from the cluster even though that the roles set in the Teleport UI should provide such access. This still need to be fixed.

- Have this done automatically in terraform-learning repo.

- Do another example of deploying teleport in minikube to avoid costs with EKS.


## References

- https://docs.aws.amazon.com/eks/latest/userguide/getting-started.html
- https://github.com/weaveworks/eksctl/blob/main/README.md#installation 
- https://docs.aws.amazon.com/eks/latest/userguide/getting-started-eksctl.html
- https://goteleport.com/docs/deploy-a-cluster/helm-deployments/aws/
- https://goteleport.com/docs/access-controls/getting-started/
