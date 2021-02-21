## Azure AKS Test

### Account
- https://azure.microsoft.com/pt-br/free

### Create AKS cluster
- https://docs.microsoft.com/en-us/azure/aks/ingress-tls

- Ubuntu Install:
```
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```
- Login and create:
```

az login (abre o navegador)

az group create --name LIVE --location eastus

az group list

az aks create --resource-group LIVE --name LIVE-AKS --node-count 3 --enable-addons monitoring --generate-ssh-key

sudo az aks install-cli

az aks get-credentials --resource-group LIVE --name LIVE-AKS

```