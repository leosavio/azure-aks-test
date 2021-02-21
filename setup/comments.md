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

```
- info status restart and time
```
kubectl get pods -n ingress-basic
```
- Replace "name-pod" with name on previous command (up) - info detail
```
kubectl describe pods -n ingress-basic "name-pod" 
```
 
 ### DNS - Lets Encrypt

 kubectl get svc -n ingress-basic (services)

## pegar comando yaml < 1:09:01

## Creating variables

kubectl get svc -n ingress-basic "name-services" (name command up - services - external ip)

export IP="X.X.X.X"
export DNSNAME="giropops-live"

export PUBLICIPID=$(az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '$IP')].[id]" --output tsv)

echo ... test all

az network public-ip update --ids $PUBLICIPID --dns-name $DNSNAME

az network public-ip show --ids $PUBLICIPID --query "[dnsSettings.fqdn]" --output tsv


## LEts encrypt

kubectl label namespace ingress-basic cert-manager.io/disable-validation=true

helm repo add jetstack https://charts.jetstack.io

helm repo update

```
helm install cert-manager jetstack/cert-manager \
  --namespace ingress-basic \
  --version v0.16.1 \
  --set installCRDs=true \
  --set nodeSelector."kubernetes\.io/os"=linux \
  --set webhook.nodeSelector."kubernetes\.io/os"=linux \
  --set cainjector.nodeSelector."kubernetes\.io/os"=linux
```

## Creating cluster Issuer
cluster-issuer.yaml:
```
apiVersion: cert-manager.io/v1alpha2
kind: ClusterIssuer
metadata:
  name: letsencrypt
spec:
  acme:
    server: https://acme-v02.api.letsencrypt.org/directory
    email: leosavio@leosavio.com
    privateKeySecretRef:
      name: letsencrypt
    solvers:
    - http01:
        ingress:
          class: nginx
          podTemplate:
            spec:
              nodeSelector:
                "kubernetes.io/os": linux
```

kubectl apply -f cluster-issuer.yaml

kubectl get clusterissuers.cert-manager.io
NAME            READY     AGE
letsencrypt     True      33s

kubectl describe clusterissuers.cert-manager.io (more details)