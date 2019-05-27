---
title: Configurer la mise en réseau kubenet dans Azure Kubernetes Service (AKS)
description: Découvrez comment configurer le réseau kubenet (de base) dans Azure Kubernetes Service (AKS) pour déployer un cluster AKS dans un réseau virtuel et un sous-réseau existants.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 01/31/2019
ms.author: iainfou
ms.reviewer: nieberts, jomore
ms.openlocfilehash: a4ed3ec823982bf3977edf9939d98419e1c4b01f
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2019
ms.locfileid: "65956392"
---
# <a name="use-kubenet-networking-with-your-own-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Utiliser la mise en réseau kubenet avec vos propres plages d’adresses IP dans Azure Kubernetes Service (AKS)

Par défaut, les clusters AKS utilisent [kubenet][kubenet]. Par ailleurs, un réseau et un sous-réseau virtuels Azure sont automatiquement créés. Avec *kubenet*, les nœuds obtiennent une adresse IP du sous-réseau du réseau virtuel Azure. Les pods reçoivent une adresse IP du sous-réseau de réseau virtuel Azure des nœuds à partir d’un espace d’adressage logiquement différent. La traduction d’adresses réseau (NAT) est ensuite configurée afin que les pods puissent accéder aux ressources sur le réseau virtuel Azure. L’adresse IP source du trafic fait l’objet d’une opération NAT sur l’adresse IP principale du nœud. Cette approche réduit considérablement le nombre d’adresses IP que vous devez réserver dans votre espace réseau pour que les pods les utilisent.

Avec l’interface [Azure Container Networking Interface (CNI)][cni-networking], chaque pod reçoit une adresse IP du sous-réseau et est accessible directement. Ces adresses IP doivent être uniques dans votre espace réseau, et être planifiées à l’avance. Chaque nœud possède un paramètre de configuration pour le nombre maximal de pods qu’il prend en charge. Le nombre équivalent d’adresses IP par nœud est alors réservé à l’avance pour ce nœud. Cette approche nécessite davantage de planification. De plus, elle conduit souvent à l’épuisement des adresses IP ou à la nécessité de regénérer les clusters dans un sous-réseau plus vaste à mesure que vos demandes d’applications augmentent.

Cet article vous montre comment utiliser la mise en réseau *kubenet* pour créer et utiliser un sous-réseau de réseau virtuel pour un cluster AKS. Pour plus d’informations sur les options et considérations relatives aux réseaux, consultez [Concepts de réseau pour Kubernetes et AKS][aks-network-concepts].

> [!WARNING]
> Pour utiliser des pools de nœuds Windows Server (actuellement en version préliminaire dans ACS), vous devez utiliser Azure CNI. L’utilisation de kubenet comme le modèle de réseau n’est pas disponible pour les conteneurs Windows Server.

## <a name="before-you-begin"></a>Avant de commencer

Azure CLI 2.0.56 (ou version ultérieure) doit être installé et configuré. Exécutez  `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez  [Installation d’Azure CLI 2.0][install-azure-cli].

## <a name="overview-of-kubenet-networking-with-your-own-subnet"></a>Vue d’ensemble de la mise en réseau kubenet avec votre propre sous-réseau

Dans de nombreux environnements, vous avez défini des réseaux et des sous-réseaux virtuels avec des plages d’adresses IP allouées. Ces ressources de réseau virtuel sont utilisées pour prendre en charge plusieurs services et applications. Pour fournir une connectivité réseau, les clusters AKS peuvent utiliser *kubenet* (mise en réseau de base) ou Azure CNI (*mise en réseau avancée*).

Avec *kubenet*, seuls les nœuds reçoivent une adresse IP dans le sous-réseau de réseau virtuel. Les pods ne peuvent pas communiquer directement entre eux. Au lieu de cela, les routes définies par l’utilisateur (UDR) et le transfert IP sont utilisés pour la connectivité entre les pods sur les nœuds. Vous pouvez également déployer des pods derrière un service qui reçoit une adresse IP attribuée et équilibre la charge du trafic pour l’application. Le diagramme suivant illustre la façon dont les nœuds AKS reçoivent une adresse IP dans le sous-réseau de réseau virtuel, mais pas les pods :

![Modèle de réseau kubenet avec un cluster AKS](media/use-kubenet/kubenet-overview.png)

Azure prend en charge un maximum de 400 routes dans une route UDR. Vous ne pouvez donc pas avoir un cluster AKS comportant plus de 400 nœuds. Les fonctionnalités AKS telles que les [nœuds virtuels][virtual-nodes] ou les stratégies réseau ne sont pas prises en charge avec *kubenet*.

Avec *Azure CNI*, chaque pod reçoit une adresse IP dans le sous-réseau IP et peut communiquer directement avec d’autres pods et services. La taille de vos clusters peut être identique à la plage d’adresses IP que vous spécifiez. Toutefois, la plage d’adresses IP doit être planifiée à l’avance, et toutes les adresses IP sont consommées par les nœuds AKS en fonction du nombre maximal de pods qu’ils peuvent prendre en charge. Les scénarios et fonctionnalités réseau avancé comme les [nœuds virtuels][virtual-nodes] ou les stratégies réseau sont pris en charge avec *Azure CNI*.

### <a name="ip-address-availability-and-exhaustion"></a>Disponibilité et épuisement des adresses IP

Avec *Azure CNI*, une plage d’adresses IP attribuée trop petite pour ensuite ajouter des nœuds supplémentaires quand vous mettez à l’échelle ou mettez à niveau un cluster constitue un problème courant. L’équipe réseau peut également ne pas être en mesure d’émettre une plage d’adresses IP suffisamment grande pour prendre en charge vos demandes d’applications attendues.

À titre de compromis, vous pouvez créer un cluster AKS qui utilise *kubenet* et vous connecter à un sous-réseau de réseau virtuel existant. Cette approche permet aux nœuds de recevoir des adresses IP définies sans avoir besoin de réserver un grand nombre d’adresses IP à l’avance pour tous les pods potentiels qui pourraient s’exécuter dans le cluster.

Avec *kubenet*, vous pouvez utiliser une plage d’adresses IP beaucoup plus petite et prendre en charge de grands clusters et les demandes d’applications. Par exemple, même avec une plage d’adresses IP  */27*, vous pouvez exécuter un cluster de 20 à 25 nœuds avec suffisamment de place pour effectuer une mise à l’échelle ou une mise à niveau. Cette taille de cluster prend en charge jusqu’à *2 200 à 2 750* pods (avec un maximum par défaut de 110 pods par nœud).

Les calculs de base suivants comparent la différence entre les modèles de réseaux :

- **kubenet** : une simple plage d’adresses IP  */24* peut prendre en charge jusqu’à *251* nœuds dans le cluster (chaque sous-réseau de réseau virtuel Azure réserve les trois premières adresses IP pour les opérations de gestion)
  - Ce nombre de nœuds peut prendre en charge jusqu’à *27 610* pods (avec un maximum par défaut de 110 pods par nœud avec *kubenet*)
- **Azure CNI** : cette même plage de sous-réseau  */24* de base peut seulement prendre en charge un maximum de *8* nœuds dans le cluster
  - Ce nombre de nœuds peut seulement prendre en charge jusqu’à *240* pods (avec un maximum par défaut de 30 pods par nœud avec *Azure CNI*)

> [!NOTE]
> Ces valeurs maximales ne prennent pas en compte les opérations de mise à niveau ou de mise à l’échelle. Dans la pratique, vous ne pouvez pas exécuter le nombre maximal de nœuds que la plage d’adresses IP de sous-réseau prend en charge. Vous devez laisser certaines adresses IP disponibles pour qu’elles puissent être utilisées pendant les opérations de mise à l’échelle ou de mise à niveau.

### <a name="virtual-network-peering-and-expressroute-connections"></a>Appairage de réseau virtuel et connexions ExpressRoute

Pour fournir une connectivité locale, les approches des réseaux *kubenet* et *Azure CNI* peuvent toutes les deux utiliser l’[appairage de réseau virtuel Azure][vnet-peering] ou les [connexions ExpressRoute][express-route]. Planifiez vos plages d’adresses IP avec soin pour éviter le chevauchement et un routage incorrect du trafic. Par exemple, de nombreux réseaux locaux utilisent une plage d’adresses *10.0.0.0/8* qui est publiée sur la connexion ExpressRoute. Il est recommandé de créer vos clusters AKS dans des sous-réseaux de réseau virtuel Azure en dehors de cette plage d’adresses, comme *172.26.0.0/16*.

### <a name="choose-a-network-model-to-use"></a>Choisir un modèle de réseau à utiliser

Le choix du plug-in réseau à utiliser pour votre cluster AKS est généralement un équilibre entre les besoins de flexibilité et les besoins de configuration avancée. Les considérations suivantes aident à déterminer quand chaque modèle de réseau peut être le plus approprié.

Utilisez *kubenet* quand :

- Vous avez un espace d’adressage IP limité.
- La majeure partie de la communication des pods s’effectue dans le cluster.
- Vous n’avez pas besoin de fonctionnalités avancées comme des nœuds virtuels ou une stratégie réseau.

Utilisez *Azure CNI* quand :

- Vous disposez d’espace d’adressage IP disponible.
- La majeure partie de la communication des pods s’effectue avec des ressources hors du cluster.
- Vous ne voulez pas gérer les routes UDR.
- Vous avez besoin de fonctionnalités avancées comme des nœuds virtuels ou une stratégie réseau.

> [!NOTE]
> Kuberouter permet d’activer la stratégie de réseau lors de l’utilisation de kubenet et peut être installé en tant que daemonset dans un cluster AKS. Sachez kube-routeur est toujours en version bêta et aucune prise en charge n’est proposé par Microsoft pour le projet.

## <a name="create-a-virtual-network-and-subnet"></a>Créer un réseau virtuel et un sous-réseau

Pour bien commencer avec l’utilisation de *kubenet* et de votre propre sous-réseau de réseau virtuel, commencez par créer un groupe de ressources à l’aide de la commande [az group create][az-group-create]. L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *eastus* :

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Si vous n’avez pas de réseau virtuel et de sous-réseau existants à utiliser, créez ces ressources réseau à l’aide de la commande [az network vnet create][az-network-vnet-create]. Dans l’exemple suivant, le réseau virtuel est nommé *myVnet* avec le préfixe d’adresse *10.0.0.0/8*. Un sous-réseau appelé *myAKSSubnet* avec le préfixe d’adresse *10.240.0.0/16* est créé.

```azurecli-interactive
az network vnet create \
    --resource-group myResourceGroup \
    --name myAKSVnet \
    --address-prefixes 10.0.0.0/8 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 10.240.0.0/16
```

## <a name="create-a-service-principal-and-assign-permissions"></a>Créer un principal de service et attribuer des autorisations

Pour permettre à un cluster AKS d’interagir avec d’autres ressources Azure, un principal du service Azure Active Directory est utilisé. Le principal du service doit disposer d’autorisations pour gérer le réseau virtuel et le sous-réseau que les nœuds AKS utilisent. Pour créer un principal de service, utilisez la commande [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] :

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

L’exemple de sortie suivant montre l’ID d’application et le mot de passe de votre principal de service. Ces valeurs sont utilisées dans des étapes supplémentaires pour attribuer un rôle au principal de service, puis créer le cluster AKS :

```console
$ az ad sp create-for-rbac --skip-assignment

{
  "appId": "476b3636-5eda-4c0e-9751-849e70b5cfad",
  "displayName": "azure-cli-2019-01-09-22-29-24",
  "name": "http://azure-cli-2019-01-09-22-29-24",
  "password": "a1024cd7-af7b-469f-8fd7-b293ecbb174e",
  "tenant": "72f998bf-85f1-41cf-92ab-2e7cd014db46"
}
```

Pour attribuer les délégations correctes dans les étapes restantes, utilisez les commandes [az network vnet show][az-network-vnet-show] et [az network vnet subnet show][az-network-vnet-subnet-show] pour obtenir les ID de ressource nécessaires. Ces ID de ressource sont stockés sous forme de variables et référencés dans les étapes restantes :

```azurecli-interactive
VNET_ID=$(az network vnet show --resource-group myResourceGroup --name myAKSVnet --query id -o tsv)
SUBNET_ID=$(az network vnet subnet show --resource-group myResourceGroup --vnet-name myAKSVnet --name myAKSSubnet --query id -o tsv)
```

Affectez maintenant le principal de service pour les autorisations de *Contributeur* de votre cluster AKS sur le réseau virtuel à l’aide de la commande [az role assignment create][az-role-assignment-create]. Fournissez votre propre  *\<appId >* comme indiqué dans la sortie de la commande précédente pour créer le principal du service :

```azurecli-interactive
az role assignment create --assignee <appId> --scope $VNET_ID --role Contributor
```

## <a name="create-an-aks-cluster-in-the-virtual-network"></a>Créer un cluster AKS dans le réseau virtuel

Vous venez de créer un réseau virtuel et un sous-réseau. Vous avez également créé et attribué des autorisations pour un principal de service afin d’utiliser ces ressources réseau. Créez maintenant un cluster AKS dans votre réseau virtuel et sous-réseau à l’aide la commande [az aks create][az-aks-create]. Définir votre propre principal de service  *\<appId >* et  *\<mot de passe >*, comme illustré dans la sortie de la commande précédente pour créer le principal du service.

Les plages d’adresses IP suivantes sont également définies dans le cadre du processus de création du cluster :

* *--service-cidr* est utilisée pour affecter des services internes dans l’adresse IP d’un cluster AKS. Cette plage d’adresses IP doit être un espace d’adressage qui n’est pas utilisé ailleurs dans votre environnement réseau. Cela inclut les plages de réseau local si vous connectez ou envisagez de connecter vos réseaux virtuels Azure à l’aide d’Express Route ou d’une connexion VPN de site à site.

* L’adresse *--dns-service-ip* doit être l’adresse  *.10* de la plage d’adresses IP de votre service.

* *--pod-cidr* doit être un grand espace d’adressage qui n’est pas utilisé ailleurs dans votre environnement réseau. Cela inclut les plages de réseau local si vous connectez ou envisagez de connecter vos réseaux virtuels Azure à l’aide d’Express Route ou d’une connexion VPN de site à site.
    * Cette plage d’adresses doit être suffisamment grande pour contenir le nombre de nœuds que vous prévoyez d’obtenir par le biais d’un scale-up. Vous ne pouvez pas changer cette plage d’adresses une fois le cluster déployé si vous avez besoin de davantage d’adresses pour des nœuds supplémentaires.
    * La plage d’adresses IP de pod est utilisée pour attribuer un espace d’adressage  */24* pour chaque nœud du cluster. Dans l’exemple suivant, l’adresse *--pod-cidr* *192.168.0.0/16* attribue le premier nœud *192.168.0.0/24*, le deuxième nœud *192.168.1.0/24* et le troisième nœud *192.168.2.0/24*.
    * À mesure que le cluster est mis à l’échelle ou à niveau, la plateforme Azure continue d’attribuer une plage d’adresses IP de pod à chaque nouveau nœud.
    
* Le *--adresse du pont docker* permet aux nœuds AKS de communiquer avec la plateforme de gestion sous-jacente. Cette adresse IP ne doit pas être dans la plage d’adresses IP du réseau virtuel de votre cluster, ni chevaucher d’autres plages d’adresses actuellement utilisées sur votre réseau.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 3 \
    --network-plugin kubenet \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --pod-cidr 192.168.0.0/16 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal <appId> \
    --client-secret <password>
```

Quand vous créez un cluster AKS, un groupe de sécurité réseau et une table de routage sont créés. Ces ressources sont gérées par le plan de contrôle AKS. Le groupe de sécurité réseau est automatiquement associé avec les cartes réseau virtuelles sur vos nœuds. La table de routage est automatiquement associée au sous-réseau de réseau virtuel. Règles de groupe de sécurité réseau et les tables de routage et sont automatiquement mis à jour quand vous créez et exposez des services.

## <a name="next-steps"></a>Étapes suivantes

Maintenant qu’un cluster AKS est déployé dans votre sous-réseau de réseau virtuel existant, vous pouvez utiliser le cluster comme d’habitude. Commencez avec la [génération d’applications à l’aide d’Azure Dev Spaces][dev-spaces] ou l’[utilisation de Draft][use-draft], ou bien [déployez des applications à l’aide de Helm][use-helm].

<!-- LINKS - External -->
[dev-spaces]: https://docs.microsoft.com/azure/dev-spaces/
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet

<!-- LINKS - Internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-network-concepts]: concepts-network.md
[az-group-create]: /cli/azure/group#az-group-create
[az-network-vnet-create]: /cli/azure/network/vnet#az-network-vnet-create
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-network-vnet-show]: /cli/azure/network/vnet#az-network-vnet-show
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-aks-create]: /cli/azure/aks#az-aks-create
[use-helm]: kubernetes-helm.md
[use-draft]: kubernetes-draft.md
[virtual-nodes]: virtual-nodes-cli.md
[vnet-peering]: ../virtual-network/virtual-network-peering-overview.md
[express-route]: ../expressroute/expressroute-introduction.md
