# ArgoCD_Minikube
Compilation of steps required to install and setup argoCD on Minikube

> ## 1. Install Argo CD¶


```
kubectl create namespace argocd

kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml


```
This will create a new namespace, argocd, where Argo CD services and application resources will live.

Warning
The installation manifests include ClusterRoleBinding resources that reference argocd namespace. If you are installing Argo CD into a different namespace then make sure to update the namespace reference.

If you are not interested in UI, SSO, multi-cluster features then you can install core Argo CD components only:


```

kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/core-install.yaml

```

This default installation will have a self-signed certificate and cannot be accessed without a bit of extra work. Do one of:

Follow the instructions to configure a certificate (and ensure that the client OS trusts it).
Configure the client OS to trust the self signed certificate.

Use the --insecure flag on all Argo CD CLI operations in this guide.

Use argocd login --core to configure CLI access and skip steps 3-5.

> ## 2. Download Argo CD CLI¶

Download the latest Argo CD version from https://github.com/argoproj/argo-cd/releases/latest. More detailed installation instructions can be found via the CLI installation documentation.

Also available in Mac, Linux and WSL Homebrew:


```
brew install argocd

```
> ## 3. Access The Argo CD API Server¶

By default, the Argo CD API server is not exposed with an external IP. To access the API server, choose one of the following techniques to expose the Argo CD API server:

Service Type Load Balancer¶

Change the argocd-server service type to LoadBalancer:

```
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'

```
Ingress¶

Follow the ingress documentation on how to configure Argo CD with ingress.

### Port Forwarding¶

```
Kubectl port-forwarding can also be used to connect to the API server without exposing the service.

kubectl port-forward svc/argocd-server -n argocd 8080:443
```

The API server can then be accessed using https://localhost:8080

> ## 4. Login Using The CLI¶

The initial password for the admin account is auto-generated and stored as clear text in the field password in a secret named argocd-initial-admin-secret in your Argo CD installation namespace. You can simply retrieve this password using kubectl:

```
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo

```
