---
title: Utiliser une adresse IP statique avec l’équilibrage de charge d’Azure Kubernetes Service (AKS)
description: Utiliser une adresse IP statique avec l’équilibrage de charge d’Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/21/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 87fe014d5c19be675d4f6cac876548a31a4484b4
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/17/2018
ms.locfileid: "42140483"
---
# <a name="use-a-static-ip-address-with-the-azure-kubernetes-service-aks-load-balancer"></a>Utiliser une adresse IP statique avec l’équilibrage de charge d’Azure Kubernetes Service (AKS)

Dans certains cas, comme lorsque l’équilibrage de charge d’Azure Kubernetes Service (AKS) est recréé ou que les services Kubernetes avec un type LoadBalancer sont recréés, l’adresse IP publique du service Kubernetes peut changer. Ce document décrit en détail la configuration d’une adresse IP statique pour vos services Kubernetes.

## <a name="create-static-ip-address"></a>Créer une adresse IP statique

Créez une adresse IP publique statique pour le service Kubernetes. L’adresse IP doit être créée dans le groupe de ressources du **nœud** AKS. Obtenez le nom du groupe de ressources avec la commande [az resource show][az-resource-show].

```azurecli-interactive
$ az resource show --resource-group myResourceGroup --name myAKSCluster --resource-type Microsoft.ContainerService/managedClusters --query properties.nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Utilisez la commande [az network public ip create][az-network-public-ip-create] pour créer l’adresse IP.

```azurecli-interactive
az network public-ip create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP --allocation-method static
```

Notez l’adresse IP.

```json
{
  "publicIp": {
    "dnsSettings": null,
    "etag": "W/\"6b6fb15c-5281-4f64-b332-8f68f46e1358\"",
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myAKSPublicIP",
    "idleTimeoutInMinutes": 4,
    "ipAddress": "40.121.183.52",
    "ipConfiguration": null,
    "ipTags": [],
    "location": "eastus",
    "name": "myAKSPublicIP",
    "provisioningState": "Succeeded",
    "publicIpAddressVersion": "IPv4",
    "publicIpAllocationMethod": "Static",
    "resourceGroup": "myResourceGroup",
    "resourceGuid": "56ec8760-a3b8-4aeb-a89d-42e68d2cbc8c",
    "sku": {
      "name": "Basic"
    },
    "tags": null,
    "type": "Microsoft.Network/publicIPAddresses",
    "zones": null
  }
````

 Si nécessaire, l’adresse peut être récupérée à l’aide de la commande [az network public-ip list][az-network-public-ip-list].

```azurecli-interactive
az network public-ip list --resource-group MC_myResourceGroup_myAKSCluster_eastus --query [0].ipAddress --output tsv
```

```console
40.121.183.52
```

## <a name="create-service-with-ip-address"></a>Créer un service avec l’adresse IP

Une fois que l’adresse IP statique a été configurée, un service Kubernetes peut être créé avec la propriété `loadBalancerIP` et une valeur de l’adresse IP statique.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  loadBalancerIP: 40.121.183.52
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

## <a name="troubleshooting"></a>Résolution de problèmes

Si l’adresse IP statique n’a pas été créée ou a été créée dans le mauvais groupe de ressources, la création du service échoue. Pour résoudre les problèmes, renvoyez les événements de création de service à l’aide de la commande [kubectl describe][kubectl-describe].

```azurecli-interactive
kubectl describe service azure-vote-front
```

```console
Name:                     azure-vote-front
Namespace:                default
Labels:                   <none>
Annotations:              <none>
Selector:                 app=azure-vote-front
Type:                     LoadBalancer
IP:                       10.0.18.125
IP:                       40.121.183.52
Port:                     <unset>  80/TCP
TargetPort:               80/TCP
NodePort:                 <unset>  32582/TCP
Endpoints:                <none>
Session Affinity:         None
External Traffic Policy:  Cluster
Events:
  Type     Reason                      Age               From                Message
  ----     ------                      ----              ----                -------
  Normal   CreatingLoadBalancer        7s (x2 over 22s)  service-controller  Creating load balancer
  Warning  CreatingLoadBalancerFailed  6s (x2 over 12s)  service-controller  Error creating load balancer (will retry): Failed to create load balancer for service default/azure-vote-front: user supplied IP Address 40.121.183.52 was not found
```

<!-- LINKS - External -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
<!-- LINKS - Internal -->
[aks-faq-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[az-resource-show]: /cli/azure/resource#az-resource-show
