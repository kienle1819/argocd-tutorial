# ArgoCD Tutorial
CI-CD, especially the CD part with the free and open-source Argo CD. 

We will use the free and open-source software ArgoCD.

- Project Homepage: https://argoproj.github.io/cd/
- Documentation: https://argo-cd.readthedocs.io/en/stable/

## Prerequisites

- Kubernetes Cluster running k3s (v1.23.6 or newer)
- Traefik (v2.5 or newer), Cert-Manager with ClusterIssuer configured
- Kubectl configured
- Public Git Repository on GitHub

*You can still use ArgoCD on other Kubernetes Clusteres like AKS, EKS, GKE, etc.

## Install and configure ArgoCD

### Install ArgoCD on Kubernetes

Create a new namespace `argocd` and deploy ArgoCD with the web UI included.

```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

### Expose ArgoCD with Traefik

Create a new IngressRoute object, follow the template described in `traefik-ingressroute.yml`.

### Disable internal TLS

Edit the --insecure flag in the argocd-server command of the argocd-server deployment.

```bash
kubectl -n argocd edit deployment.apps argocd-server
```

Change the container command from:
```yml
    ...
    containers:
    - command:
      - argocd-server
     ...
```

To:
```yml
    ...
    containers:
    - command:
      - argocd-server
      - --insecure      
    ...
```

### Log in to the ArgoCD web interface

Log in to the ArgoCD web interface `https://<your-dns-record>/` by using the default username `admin` and the password, collected by the following command.

```bash
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```

## Add a Git Repository

Add your Git Repository in the **Settings -> Repositories** menu.

## Create an Application in ArgoCD

Create an Application in ArgoCD to deploy your Git Repository in Kubernetes
