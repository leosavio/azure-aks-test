# Azure AKS Test

## Account
- https://azure.microsoft.com/pt-br/free

## Create AKS cluster
- https://docs.microsoft.com/en-us/azure/aks/ingress-tls

- Ubuntu Install:
```
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

- Mac Os Install:
```
brew update && brew install azure-cli
```

- Login and create:
```

az login (abre o navegador)

az group create --name LIVE --location eastus

az group list
```
- Register provider : Microsoft.ContainerService
```
az provider register -n Microsoft.ContainerService
az provider show -n Microsoft.ContainerService

```
- Creating
```

az aks create --resource-group LIVE --name LIVE-AKS --node-count 2 --enable-addons monitoring --generate-ssh-key

sudo az aks install-cli

az aks get-credentials --resource-group LIVE --name LIVE-AKS

```

## Kubernetes
- Get nodes ready
```
kubectl get nodes 

kubectl get pods --all-namespaces

kubectl get pods --all-namespaces -o wide
```
- Metrics cpu ram
```
kubectl top nodes  

kubectl top pods --all-namespaces
```
- Helm install
```
curl https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3 | bash

```
- Ingress
```
kubectl create namespace ingress-basic

helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx

## Use Helm to deploy an NGINX ingress controller
helm install nginx-ingress ingress-nginx/ingress-nginx \
    --namespace ingress-basic \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set controller.admissionWebhooks.patch.nodeSelector."beta\.kubernetes\.io/os"=linux

	
kubectl get pods -n ingress-basic (info status restart and time)

kubectl describe pods -n ingress-basic "name-pod" (name command up - info detail)
```