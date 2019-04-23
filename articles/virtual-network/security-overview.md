---
title: Vue d’ensemble des groupes de sécurité Azure
titlesuffix: Azure Virtual Network
description: En savoir plus sur les groupes de sécurité réseau et d’application. Les groupes de sécurité vous permettent de filtrer le trafic réseau entre les ressources Azure.
services: virtual-network
documentationcenter: na
author: malopMSFT
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/26/2018
ms.author: malop;kumud
ms.openlocfilehash: 73664359b206a9e149ebac6859df24a1263cd313
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2019
ms.locfileid: "59996779"
---
# <a name="security-groups"></a>Groupes de sécurité
<a name="network-security-groups"></a>

Vous pouvez filtrer le trafic réseau depuis et vers les ressources Azure dans un [réseau virtuel](virtual-networks-overview.md) Azure avec un groupe de sécurité réseau. Un groupe de sécurité réseau contient des [règles de sécurité](#security-rules) qui autorisent ou rejettent le trafic réseau entrant et sortant vers différents types de ressources Azure. Pour en savoir plus sur les ressources Azure pouvant être déployées dans un réseau virtuel et auxquelles des groupes de sécurité réseau peuvent être associées, consultez [Intégration d’un réseau virtuel pour les services Azure](virtual-network-for-azure-services.md). Pour chaque règle, vous pouvez spécifier la source et la destination, le port et le protocole.

Cet article explique les concepts de groupe de sécurité réseau, pour vous aider à les utiliser efficacement. Si vous n’avez jamais créé un groupe de sécurité réseau, vous pouvez suivre un [didacticiel](tutorial-filter-network-traffic.md) rapide pour vous entraîner. Si vous êtes familier avec les groupes de sécurité réseau et que vous devez en gérer un, consultez [Gérer un groupe de sécurité réseau](manage-network-security-group.md). Si vous rencontrez des problèmes de communication et que vous avez besoin résoudre les problèmes de groupes de sécurité réseau, consultez [Diagnostiquer un problème de filtre de trafic réseau sur une machine virtuelle](diagnose-network-traffic-filter-problem.md). Vous pouvez activer [les journaux de flux de groupe de sécurité réseau](../network-watcher/network-watcher-nsg-flow-logging-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) afin d’[analyser le trafic réseau](../network-watcher/traffic-analytics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vers et à partir des ressources auxquelles un groupe de sécurité réseau est associé.

## <a name="security-rules"></a>Règles de sécurité

Un groupe de sécurité réseau contient le nombre des règles souhaité (ou aucune), dans les [limites](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) de l’abonnement Azure. Chaque règle spécifie les propriétés suivantes :

|Propriété  |Explication  |
|---------|---------|
|Name|Nom unique au sein du groupe de sécurité réseau.|
|Priorité | Nombre compris entre 100 et 4096. Les règles sont traitées dans l’ordre croissant, car les nombres les plus faibles sont prioritaires. Une fois que le trafic correspond à une règle, le traitement s’arrête. Par conséquent, les règles avec des priorités plus faibles (des nombres plus élevés) et ayant les mêmes attributs que les règles de priorité supérieure ne sont pas traitées.|
|Source ou destination| Tout, ou adresse IP, bloc de routage CIDR (10.0.0.0/24, par exemple), une [balise de service](#service-tags) ou [groupe de sécurité d’application](#application-security-groups) individuel(le). Si vous spécifiez une adresse pour une ressource Azure, spécifiez l’adresse IP privée assignée à la ressource. Les groupes de sécurité réseau sont traités une fois qu’Azure a converti une adresse IP publique en adresse IP privée pour le trafic entrant, et avant qu’Azure ne convertisse une adresse IP privée en une adresse IP publique pour le trafic sortant. En savoir plus sur les [adresses IP](virtual-network-ip-addresses-overview-arm.md) Azure. En spécifiant une plage, une balise de service ou un groupe de sécurité d’application, vous pouvez créer moins des règles de sécurité. La possibilité de spécifier plusieurs adresses IP individuelles et plages (vous ne pouvez pas spécifier plusieurs balises de service ou groupes d’applications) dans une règle est désignée sous le nom de [règles de sécurité augmentée](#augmented-security-rules). Les règles de sécurité augmentée peuvent uniquement être créées dans des groupes de sécurité réseau créés par le biais du modèle de déploiement du Gestionnaire de ressources. Vous ne pouvez pas spécifier plusieurs adresses IP et plages d’adresses IP dans les groupes de sécurité réseau créés par le biais du modèle de déploiement classique. En savoir plus sur les [modèles de déploiement Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).|
|Protocole     | TCP, UDP ou Tout, ce qui inclut (sans s'y limiter) TCP, UDP et ICMP. Vous ne pouvez pas spécifier ICMP seul. Si vous avez besoin d’ICMP, vous devez donc utiliser Tout. |
|Direction| Indique si la règle s’applique au trafic entrant ou sortant.|
|Plage de ports     |Vous pouvez spécifier un port individuel ou une plage de ports. Par exemple, indiquez 80 ou 10000-10005. La spécification de plages vous permet de créer moins de règles de sécurité. Les règles de sécurité augmentée peuvent uniquement être créées dans des groupes de sécurité réseau créés par le biais du modèle de déploiement du Gestionnaire de ressources. Vous ne pouvez pas spécifier plusieurs ports ou plages de ports dans les groupes de sécurité réseau créés par le biais du modèle de déploiement classique.   |
|Action     | Autoriser ou refuser        |

Les règles de sécurité des groupes de sécurité réseau sont évaluées par priorité selon un tuple à 5 éléments (source, port source, destination, port de destination et protocole) pour autoriser ou refuser le trafic. Un enregistrement de flux est créé pour les connexions existantes. La communication est autorisée ou refusée en fonction de l’état de connexion de l’enregistrement de flux. L’enregistrement de flux permet d’obtenir un groupe de sécurité réseau avec état. Si vous spécifiez une règle de sécurité sortante vers n’importe quelle adresse sur le port 80, par exemple, il n’est pas nécessaire d’indiquer une règle de sécurité entrante pour la réponse au trafic sortant. Vous devez uniquement spécifier une règle de sécurité entrante si la communication est établie en externe. Le contraire est également vrai. Si le trafic entrant est autorisé sur un port, il n’est pas nécessaire de spécifier une règle de sécurité sortante pour répondre au trafic sur ce port.
Les connexions existantes ne peuvent pas être interrompues quand vous supprimez une règle de sécurité ayant activé le flux. Les flux de trafic sont interrompus quand les connexions sont arrêtées et qu’aucun trafic ne transite dans un sens ou dans l’autre pendant au moins quelques minutes.

Le nombre de règles de sécurité que vous pouvez créer dans un groupe de sécurité réseau est limité. Pour plus d’informations, consultez [limites Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="augmented-security-rules"></a>Règles de sécurité augmentée

Les règles de sécurité augmentée simplifient la définition de la sécurité pour les réseaux virtuels, ce qui vous permet de définir des stratégies de sécurité réseau plus vastes et plus complexes, avec moins de règles. Vous pouvez combiner plusieurs ports ainsi que des adresses ou plages d’adresses IP explicites dans une seule règle de sécurité facilement compréhensible. Utiliser des règles de sécurité augmentée dans les champs de la source, de la destination et du port d’une règle. Pour simplifier la maintenance de votre définition de règle de sécurité, combinez des règles de sécurité augmentée avec des [balises de service](#service-tags) ou des [groupes de sécurité d’application](#application-security-groups). Le nombre d’adresses, les plages et les ports que vous pouvez spécifier dans une règle sont limités. Pour plus d’informations, consultez [limites Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="service-tags"></a>Balises de service

 Une balise de service représente un groupe de préfixes d’adresses IP qui permet de simplifier la création de règles de sécurité. Vous ne peut pas créer votre propre balise de service, ni spécifier les adresses IP incluses dans une balise. Microsoft gère les préfixes d’adresse englobés par la balise de service et met à jour automatiquement la balise de service quand les adresses changent. Vous pouvez utiliser des balises de service à la place des adresses IP spécifiques lors de la création de règles de sécurité. 
 
 Vous pouvez télécharger et intégrer à un pare-feu local la liste des balises de service avec les informations de préfixe dans les publications hebdomadaires suivantes pour les clouds ci-après : [Public](https://www.microsoft.com/download/details.aspx?id=56519), [Administration des États-Unis](https://www.microsoft.com/download/details.aspx?id=57063), [Chine](https://www.microsoft.com/download/details.aspx?id=57062) et [Allemagne](https://www.microsoft.com/download/details.aspx?id=57064).

 Vous pouvez utiliser les balises de service suivantes dans la définition de règle de sécurité. Leurs noms varient légèrement selon les [modèles de déploiement Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

* **VirtualNetwork** (Resource Manager) (**VIRTUAL_NETWORK** pour le mode classique) : Cette balise inclut l’espace d’adressage de réseau virtuel (toutes les plages CIDR définies pour le réseau virtuel), tous les espaces d’adresse en local, connectés et [homologués](virtual-network-peering-overview.md) réseaux virtuels ou réseau virtuel connecté à un [virtuel passerelle de réseau](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json) et utilisés sur des préfixes d’adresse [itinéraires définis par l’utilisateur](virtual-networks-udr-overview.md).
* **AzureLoadBalancer** (Resource Manager) (**AZURE_LOADBALANCER** pour le mode classique) : Cette balise par défaut indique l’équilibreur de charge de l’infrastructure d’Azure. Elle est translatée vers l’[adresse IP virtuelle de l’hôte](security-overview.md#azure-platform-considerations) (168.63.129.16) d’où proviennent les sondes d’intégrité d’Azure. Vous pouvez remplacer cette règle si vous n’utilisez pas l’équilibreur de charge Azure.
* **Internet** (Resource Manager) (**INTERNET** pour le mode classique) : Cette balise par défaut indique l’espace d’adresse IP qui se trouve en dehors du réseau virtuel et est accessible sur les réseaux Internet publics. La plage d’adresse inclut l’[espace de l’adresse IP public d’Azure](https://www.microsoft.com/download/details.aspx?id=41653).
* **AzureCloud** (Resource Manager uniquement) : Cette balise désigne l’espace d’adressage IP pour Azure, notamment l’ensemble des [adresses IP publiques du centre de données](https://www.microsoft.com/download/details.aspx?id=41653). Si vous spécifiez *AzureCloud* comme valeur, le trafic est autorisé ou refusé pour les adresses IP publiques Azure. Si vous souhaitez uniquement autoriser l’accès à AzureCloud dans une [région](https://azure.microsoft.com/regions) spécifique, vous pouvez spécifier la région. Par exemple, pour autoriser l’accès uniquement à Azure AzureCloud dans la région USA Est, vous pouvez spécifier *AzureCloud.EastUS* en tant que balise de service. 
* **AzureTrafficManager** (Resource Manager uniquement) : Cette balise désigne l’espace d’adressage IP pour les adresses IP de sondage Azure Traffic Manager. Vous trouverez plus d’informations sur les adresses IP de sondage Traffic Manager dans les [Questions fréquentes (FAQ) sur Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs). 
* **Stockage** (Resource Manager uniquement) : Cette balise désigne l’espace d’adressage IP pour le service Stockage Azure. Si vous spécifiez *Storage* pour la valeur, le trafic est autorisé ou refusé vers le stockage. Si vous souhaitez uniquement autoriser l’accès au stockage dans une [région](https://azure.microsoft.com/regions) spécifique, vous pouvez préciser la région. Par exemple, si vous souhaitez autoriser l’accès à Azure Storage uniquement dans la région USA Est, vous pouvez spécifier *Storage.EastUS* comme balise de service. La balise représente le service, mais pas des instances du service. Par exemple, la balise représente le service Azure Storage, mais pas un compte Azure Storage spécifique. 
* **Sql** (Resource Manager uniquement) : Cette balise désigne les préfixes d’adresse de base de données SQL Azure, Azure Database pour MySQL, Azure Database pour PostgreSQL et les services Azure SQL Data Warehouse. Si vous spécifiez *Sql* pour la valeur, le trafic vers Sql est autorisé ou refusé. Si vous souhaitez uniquement autoriser l’accès à Sql dans une [région](https://azure.microsoft.com/regions) spécifique, vous pouvez préciser la région. Par exemple, si vous souhaitez autoriser l’accès à Azure SQL Database uniquement dans la région USA Est, vous pouvez spécifier *Sql.EastUS* comme balise de service. La balise représente le service, mais pas des instances du service. Par exemple, la balise représente le service Azure SQL Database, mais pas une base de données ou un serveur SQL spécifique. 
* **AzureCosmosDB** (Resource Manager uniquement) : Cette balise désigne les préfixes d’adresse du service Azure Cosmos DB. Si vous spécifiez *AzureCosmosDB* comme valeur, le trafic vers AzureCosmosDB est autorisé ou refusé. Si vous souhaitez uniquement autoriser l’accès à AzureCosmosDB dans une [région](https://azure.microsoft.com/regions) spécifique, vous pouvez spécifier la région au format suivant : AzureCosmosDB.[nom_région]. 
* **AzureKeyVault** (Resource Manager uniquement) : Cette balise désigne les préfixes d’adresse du service Azure Key Vault. Si vous spécifiez *AzureKeyVault* comme valeur, le trafic vers AzureKeyVault est autorisé ou refusé. Si vous souhaitez uniquement autoriser l’accès à AzureKeyVault dans une [région](https://azure.microsoft.com/regions) spécifique, vous pouvez spécifier la région au format suivant : AzureKeyVault.[nom_région]. 
* **EventHub** (Resource Manager uniquement) : Cette balise désigne les préfixes d’adresse du service Azure EventHub. Si vous spécifiez *EventHub* comme valeur, le trafic vers EventHub est autorisé ou refusé. Si vous souhaitez uniquement autoriser l’accès à EventHub dans une [région](https://azure.microsoft.com/regions) spécifique, vous pouvez spécifier la région au format suivant : EventHub.[nom_région]. 
* **ServiceBus** (Resource Manager uniquement) : Cette balise désigne les préfixes d’adresse du service Azure ServiceBus à l’aide de la couche de service Premium. Si vous spécifiez *ServiceBus* comme valeur, le trafic vers ServiceBus est autorisé ou refusé. Si vous souhaitez uniquement autoriser l’accès à ServiceBus dans une [région](https://azure.microsoft.com/regions) spécifique, vous pouvez spécifier la région au format suivant : ServiceBus.[nom_région]. 
* **MicrosoftContainerRegistry** (Resource Manager uniquement) : Cette balise désigne les préfixes d’adresse du service Microsoft Container Registry. Si vous spécifiez *MicrosoftContainerRegistry* comme valeur, le trafic vers MicrosoftContainerRegistry est autorisé ou refusé. Si vous souhaitez uniquement autoriser l’accès à MicrosoftContainerRegistry dans une [région](https://azure.microsoft.com/regions) spécifique, vous pouvez spécifier la région au format suivant : MicrosoftContainerRegistry.[nom_région]. 
* **AzureContainerRegistry** (Resource Manager uniquement) : Cette balise désigne les préfixes d’adresse du service Azure Container Registry. Si vous spécifiez *AzureContainerRegistry* comme valeur, le trafic vers AzureContainerRegistry est autorisé ou refusé. Si vous souhaitez uniquement autoriser l’accès à AzureContainerRegistry dans une [région](https://azure.microsoft.com/regions) spécifique, vous pouvez spécifier la région au format suivant : AzureContainerRegistry.[nom_région]. 
* **AppService** (Resource Manager uniquement) : Cette balise désigne les préfixes d’adresse du service Azure AppService. Si vous spécifiez *AppService* comme valeur, le trafic vers AppService est autorisé ou refusé. Si vous souhaitez uniquement autoriser l’accès à AppService dans une [région](https://azure.microsoft.com/regions) spécifique, vous pouvez spécifier la région au format suivant : AppService.[nom_région]. 
* **AppServiceManagement** (Resource Manager uniquement) : Cette balise désigne les préfixes d’adresse du service Azure AppService Management. Si vous spécifiez *AppServiceManagement* comme valeur, le trafic vers AppServiceManagement est autorisé ou refusé. 
* **ApiManagement** (Resource Manager uniquement) : Cette balise désigne les préfixes d’adresse du service Gestion des API Azure. Si vous spécifiez *ApiManagement* comme valeur, le trafic vers ApiManagement est autorisé ou refusé.  
* **AzureConnectors** (Resource Manager uniquement) : Cette balise désigne les préfixes d’adresse du service Azure Connectors. Si vous spécifiez *AzureConnectors* comme valeur, le trafic vers AzureConnectors est autorisé ou refusé. Si vous souhaitez uniquement autoriser l’accès à AzureConnectors dans une [région](https://azure.microsoft.com/regions) spécifique, vous pouvez spécifier la région au format suivant : AzureConnectors.[nom_région]. 
* **GatewayManager** (Resource Manager uniquement) : Cette balise désigne les préfixes d’adresse du service Azure Gateway Manager. Si vous spécifiez *GatewayManager* comme valeur, le trafic vers GatewayManager est autorisé ou refusé.  
* **AzureDataLake** (Resource Manager uniquement) : Cette balise désigne les préfixes d’adresse du service Azure Data Lake. Si vous spécifiez *AzureDataLake* comme valeur, le trafic vers AzureDataLake est autorisé ou refusé. 
* **AzureActiveDirectory** (Resource Manager uniquement) : Cette balise désigne les préfixes d’adresse du service AzureActiveDirectory. Si vous spécifiez *AzureActiveDirectory* comme valeur, le trafic vers AzureActiveDirectory est autorisé ou refusé.  
* **AzureMonitor** (Resource Manager uniquement) : Cette balise désigne les préfixes d’adresse du service AzureMonitor. Si vous spécifiez *AzureMonitor* comme valeur, le trafic vers AzureMonitor est autorisé ou refusé. 
* **ServiceFabric** (Resource Manager uniquement) : Cette balise désigne les préfixes d’adresse du service ServiceFabric. Si vous spécifiez *ServiceFabric* comme valeur, le trafic vers ServiceFabric est autorisé ou refusé. 
* **AzureMachineLearning** (Resource Manager uniquement) : Cette balise désigne les préfixes d’adresse du service AzureMachineLearning. Si vous spécifiez *AzureMachineLearning* comme valeur, le trafic est autorisé ou refusé à AzureMachineLearning. 
* **BatchNodeManagement** (Resource Manager uniquement) : Cette balise désigne les préfixes d’adresse du service Azure BatchNodeManagement. Si vous spécifiez *BatchNodeManagement* pour la valeur, le trafic est autorisé ou refusé à partir du service de traitement par lots pour les nœuds de calcul.

> [!NOTE]
> Les balises des services Azure indiquent les préfixes d’adresse du cloud spécifique utilisé. 

> [!NOTE]
> Si vous implémentez un [point de terminaison de service de réseau virtuel](virtual-network-service-endpoints-overview.md) pour un service, par exemple Stockage Azure ou Azure SQL Database, Azure ajoute un [itinéraire](virtual-networks-udr-overview.md#optional-default-routes) vers un sous-réseau de réseau virtuel pour le service. Les préfixes d’adresse de l’itinéraire sont les mêmes préfixes d’adresse ou plages CIDR que pour la balise de service correspondante.

## <a name="default-security-rules"></a>Règles de sécurité par défaut

Azure crée les règles par défaut suivantes dans chaque groupe de sécurité réseau que vous créez :

### <a name="inbound"></a>Trafic entrant

#### <a name="allowvnetinbound"></a>AllowVNetInBound

|Priorité|Source|Ports source|Destination|Ports de destination|Protocole|Access|
|---|---|---|---|---|---|---|
|65 000|VirtualNetwork|0-65535|VirtualNetwork|0-65535|Tous|AUTORISER|

#### <a name="allowazureloadbalancerinbound"></a>AllowAzureLoadBalancerInBound

|Priorité|Source|Ports source|Destination|Ports de destination|Protocole|Access|
|---|---|---|---|---|---|---|
|65 001|AzureLoadBalancer|0-65535|0.0.0.0/0|0-65535|Tous|AUTORISER|

#### <a name="denyallinbound"></a>DenyAllInbound

|Priorité|Source|Ports source|Destination|Ports de destination|Protocole|Access|
|---|---|---|---|---|---|---|
|65 500|0.0.0.0/0|0-65535|0.0.0.0/0|0-65535|Tous|Deny|

### <a name="outbound"></a>Règle de trafic sortant

#### <a name="allowvnetoutbound"></a>AllowVnetOutBound

|Priorité|Source|Ports source| Destination | Ports de destination | Protocole | Access |
|---|---|---|---|---|---|---|
| 65 000 | VirtualNetwork | 0-65535 | VirtualNetwork | 0-65535 | Tous | AUTORISER |

#### <a name="allowinternetoutbound"></a>AllowInternetOutBound

|Priorité|Source|Ports source| Destination | Ports de destination | Protocole | Access |
|---|---|---|---|---|---|---|
| 65 001 | 0.0.0.0/0 | 0-65535 | Internet | 0-65535 | Tous | AUTORISER |

#### <a name="denyalloutbound"></a>DenyAllOutBound

|Priorité|Source|Ports source| Destination | Ports de destination | Protocole | Access |
|---|---|---|---|---|---|---|
| 65 500 | 0.0.0.0/0 | 0-65535 | 0.0.0.0/0 | 0-65535 | Tous | Deny |

Dans les colonnes **Source** et **Destination**, *VirtualNetwork*, *AzureLoadBalancer* et *Internet* sont des [balises de service](#service-tags), non des adresses IP. Dans la colonne de protocole, **Tout** englobe TCP, UDP et ICMP. Lorsque vous créez une règle, vous pouvez spécifier TCP, UDP ou Tout, mais vous ne pouvez pas indiquer uniquement ICMP. Par conséquent, si votre règle requiert ICMP, sélectionnez l’option *Tout* pour le protocole. *0.0.0.0/0* dans les colonnes **Source** et **Destination** représente toutes les adresses. Les clients tels que le portail Azure, Azure CLI, ou de Powershell peut utiliser * ou any pour cette expression.
 
Vous ne pouvez pas supprimer les règles par défaut, mais vous pouvez les remplacer par des règles de priorité plus élevée.

## <a name="application-security-groups"></a>Groupes de sécurité d’application

Les groupes de sécurité d’application permettent de configurer la sécurité réseau comme un prolongement naturel de la structure de l’application, et donc de regrouper les machines virtuelles et définir des stratégies de sécurité réseau basés sur ces groupes. Vous pouvez réutiliser votre stratégie de sécurité à grande échelle sans maintenance manuelle d’adresses IP explicites. La plateforme gère la complexité des adresses IP explicites et plusieurs ensembles de règles, ce qui vous permet de vous concentrer sur la logique métier. Pour mieux comprendre les groupes de sécurité d’application, prenons l’exemple suivant :

![Groupes de sécurité d’application](./media/security-groups/application-security-groups.png)

Dans l’image précédente, *NIC1* et *NIC2* sont membres du groupe de sécurité d’application *AsgWeb*. *NIC3* est un membre du groupe de sécurité d’application *AsgLogic*. *NIC4* est un membre du groupe de sécurité d’application *AsgDb*. Bien que chaque interface réseau dans cet exemple soit membre d’un seul groupe de sécurité d’application, une interface réseau peut être membre de plusieurs groupes de sécurité d’application, jusqu'aux [limites Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Aucune de ces interfaces réseau ne dispose d’un groupe de sécurité réseau associé. *NSG1* est associé aux deux sous-réseaux et contient les règles suivantes :

### <a name="allow-http-inbound-internet"></a>Allow-HTTP-Inbound-Internet

Cette règle est nécessaire pour autoriser le trafic internet vers les serveurs web. Étant donné que le trafic entrant à partir d’internet est refusé par la règle de sécurité par défaut [DenyAllInbound](#denyallinbound), aucune règle supplémentaire n’est nécessaire pour les groupes de sécurité d’application *AsgLogic* ou *AsgDb*.

|Priorité|Source|Ports source| Destination | Ports de destination | Protocole | Access |
|---|---|---|---|---|---|---|
| 100 | Internet | * | AsgWeb | 80 | TCP | AUTORISER |

### <a name="deny-database-all"></a>Deny-Database-All

Étant donné que la règle de sécurité par défaut [AllowVNetInBound](#allowvnetinbound) autorise toutes les communications entre les ressources dans le même réseau virtuel, cette règle est nécessaire pour refuser le trafic à partir de toutes les ressources.

|Priorité|Source|Ports source| Destination | Ports de destination | Protocole | Access |
|---|---|---|---|---|---|---|
| 120 | * | * | AsgDb | 1433 | Tous | Deny |

### <a name="allow-database-businesslogic"></a>Allow-Database-BusinessLogic

Cette règle autorise le trafic du groupe de sécurité d’application *AsgLogic* vers le groupe de sécurité d’application *AsgDb*. Cette règle est prioritaire par rapport à la règle *Deny-Database-All*. Par conséquent, cette règle est traitée avant la règle *Deny-Database-All*, c’est pourquoi le trafic en provenance du groupe de sécurité d’application *AsgLogic* est autorisé, tandis que tout autre trafic est bloqué.

|Priorité|Source|Ports source| Destination | Ports de destination | Protocole | Access |
|---|---|---|---|---|---|---|
| 110 | AsgLogic | * | AsgDb | 1433 | TCP | AUTORISER |

Les règles spécifiant un groupe de sécurité d’application en tant que source ou destination sont appliquées uniquement aux interfaces réseau qui sont membres du groupe de sécurité d’application. Si l’interface réseau n’est pas membre d’un groupe de sécurité d’application, la règle n’est pas appliquée à l’interface réseau, même si le groupe de sécurité réseau est associé au sous-réseau.

Les groupes de sécurité d’application ont les contraintes suivantes :

-   Le nombre de groupes de sécurité d’application que vous pouvez avoir dans un abonnement, ainsi que d’autres paramètres relatifs aux groupes de sécurité d’application, sont limités. Pour plus d’informations, consultez [limites Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- Vous pouvez spécifier un groupe de sécurité d’application en tant que source et destination dans une règle de sécurité. Vous ne pouvez pas spécifier plusieurs groupes de sécurité d’application dans la source ou la destination.
- Toutes les interfaces réseau affectées à un groupe de sécurité d’application doivent exister dans le même réseau virtuel que celui où se trouve la première interface réseau affectée au groupe de sécurité d’application. Par exemple, si la première interface réseau assignée à un groupe de sécurité d’application nommé *AsgWeb* se trouve dans le réseau virtuel nommé *VNet1*, toutes les interfaces réseau suivantes affectées à *AsgWeb* doivent exister dans *VNet1*. Vous ne pouvez pas ajouter d’interfaces réseau à partir de différents réseaux virtuels au même groupe de sécurité d’application.
- Si vous spécifiez un groupe de sécurité d’application en tant que source et destination dans une règle de sécurité, les interfaces réseau dans les deux groupes de sécurité d’application doivent se trouver dans le même réseau virtuel. Par exemple, si *AsgLogic* contient des interfaces réseau de *VNet1*, et si *AsgDb* contient des interfaces réseau de *VNet2*, vous ne pouvez pas assigner *AsgLogic* en tant que source et *AsgDb* en tant que destination dans une règle. Toutes les interfaces réseau pour les groupes de sécurité d’application source et destination doivent exister dans le même réseau virtuel.

> [!TIP]
> Pour réduire le nombre de règles de sécurité dont vous avez besoin et la nécessité de modifier les règles, planifiez les groupes de sécurité d’application dont vous avez besoin et créez des règles à l’aide de balises de service ou des groupes de sécurité d’application, plutôt que des adresses IP individuelles ou des plages d’adresses IP, chaque fois que c’est possible.

## <a name="how-traffic-is-evaluated"></a>Évaluation du trafic

Vous pouvez déployer des ressources à partir de plusieurs services Azure dans un réseau virtuel Azure. Pour une liste complète, consultez [Services pouvant être déployés dans un réseau virtuel](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network). Vous pouvez associer zéro ou un groupe de sécurité réseau à chaque [sous-réseau](virtual-network-manage-subnet.md#change-subnet-settings) de réseau virtuel et [interface réseau](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group) dans une machine virtuelle. Vous pouvez associer le même groupe de sécurité réseau à autant d’interfaces réseau individuelles et autant de sous-réseaux que vous le souhaitez.

L’image suivante illustre les différents scénarios de déploiement des groupes de sécurité réseau afin d’autoriser le trafic réseau vers et à partir d’internet via le port TCP 80 :

![NSG-processing](./media/security-groups/nsg-interaction.png)

Référez-vous à l’image précédente ainsi qu’au texte suivant pour comprendre comment Azure traite les règles entrantes et sortantes pour les groupes de sécurité réseau :

### <a name="inbound-traffic"></a>Trafic entrant

Pour le trafic entrant, Azure traite d’abord les règles dans un groupe de sécurité réseau associées à un sous-réseau, si elles existent, puis les règles dans un groupe de sécurité réseau associées à l’interface réseau, si elles existent.

- **VM1** : Les règles de la sécurité dans *NSG1* sont traitées dans la mesure où elles sont associées à *Subnet1* et *VM1* est dans *Subnet1*. Sauf si vous avez créé une règle autorisant le trafic entrant par le port 80, le trafic est refusé par la règle de sécurité par défaut [DenyAllInbound](#denyallinbound) et n’est jamais évalué par *NSG2*, étant donné que *NSG2* est associé à l’interface réseau. Si *NSG1* dispose d’une règle de sécurité qui autorise le trafic entrant par le port 80, le trafic est ensuite traité par *NSG2*. Pour autoriser le port 80 vers la machine virtuelle, *NSG1* et *NSG2* doivent disposer tous deux d’une règle autorisant l’accès au port 80 à partir d’Internet.
- **VM2** : Les règles dans *NSG1* sont traitées étant donné que *VM2* se trouve également dans *Subnet1*. Dans la mesure où *VM2* ne dispose pas d’un groupe de sécurité réseau associé à son interface réseau, elle reçoit tout le trafic autorisé via *NSG1* ou se voit refuser tout le trafic refusé par *NSG1*. Le trafic est soit autorisé, soit refusé à toutes les ressources dans le même sous-réseau lorsqu’un groupe de sécurité réseau est associé à un sous-réseau.
- **VM3** : Dans la mesure où il n’existe aucun groupe de sécurité réseau associé à *Subnet2*, le trafic est autorisé dans le sous-réseau et traité par *NSG2*, car *NSG2* est associé à l’interface réseau attachée à *VM3*.
- **VM4** : Le trafic est autorisé vers *VM4*, car aucun groupe de sécurité réseau n’est associé à *Subnet3* ou à l’interface réseau dans la machine virtuelle. L’intégralité du trafic réseau est autorisée via une interface réseau et un sous-réseau si aucun groupe de sécurité réseau leur est associé.

### <a name="outbound-traffic"></a>Trafic sortant

Pour le trafic sortant, Azure traite d’abord les règles dans un groupe de sécurité réseau associées à une interface réseau, si elles existent, puis les règles dans un groupe de sécurité réseau associées au sous-réseau, si elles existent.

- **VM1** : Les règles de sécurité dans *NSG2* sont traitées. Sauf si vous créez une règle de sécurité qui refuse le trafic sortant vers internet au niveau du port 80, le trafic est autorisé par la règle de sécurité par défaut [AllowInternetOutbound](#allowinternetoutbound) à la fois dans *NSG1* et *NSG2*. Si *NSG2* présente une règle de sécurité qui refuse le trafic par le port 80, le trafic est refusé et n’est jamais évalué par *NSG1*. Pour refuser le port 80 à partir de la machine virtuelle, l’un au moins des groupes de sécurité réseau doit disposer d’une règle qui refuse l’accès à Internet par le port 80.
- **VM2** : L’ensemble du trafic est envoyé au sous-réseau via l’interface réseau puisque l’interface réseau attachée à *VM2* ne dispose pas d’un groupe de sécurité réseau associé. Les règles dans *NSG1* sont traitées.
- **VM3** : Si *NSG2* présente une règle de sécurité qui refuse le trafic par le port 80, le trafic est refusé. Si *NSG2* a une règle de sécurité qui autorise le trafic par le port 80, le port 80 autorise le trafic sortant vers internet, étant donné qu’un groupe de sécurité réseau n’est pas associé à *Subnet2*.
- **VM4** : L’ensemble du trafic réseau est autorisé depuis *VM4*, car aucun groupe de sécurité réseau n’est associé à *Subnet3* ou à l’interface réseau associée à la machine virtuelle.

Vous pouvez facilement afficher des règles d’agrégation appliquées à une interface réseau en consultant les [règles de sécurité efficaces](virtual-network-network-interface.md#view-effective-security-rules) relatives à une interface réseau. Vous pouvez également utiliser la fonctionnalité de [vérification du flux IP](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json) dans Azure Network Watcher pour déterminer si la communication est autorisée vers ou à partir d’une interface réseau. Le Flux IP vous indique si la communication est autorisée ou refusée, ainsi que la règle de sécurité réseau qui autorise ou refuse le trafic.

> [!NOTE]
> Groupes de sécurité réseau sont associés à des sous-réseaux ou aux machines virtuelles et services cloud déployés dans le modèle de déploiement classique et à des sous-réseaux ou interfaces réseau dans le modèle de déploiement Resource Manager. Pour en savoir plus sur les modèles de déploiement Azure, consultez l’article [Déploiement Azure Resource Manager et déploiement classique : comprendre les modèles de déploiement et l’état de vos ressources](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

> [!TIP]
> Sauf si vous avez une raison particulière, nous vous recommandons d’associer un groupe de sécurité réseau à un sous-réseau ou à une interface réseau, mais pas aux deux. Dans la mesure où les règles d’un groupe de sécurité réseau associées à un sous-réseau peut entrer en conflit avec des règles d’un groupe de sécurité réseau associées à une interface réseau, vous pourrez subir des problèmes de communication inattendus qui nécessiteront une intervention.

## <a name="azure-platform-considerations"></a>Considérations relatives à la plateforme Azure

- **Adresse IP virtuelle du nœud hôte** : Des services d’infrastructure de base tels que DHCP, DNS, IMDS et la surveillance de l’intégrité sont fournis par le biais des adresses IP d’hôte virtualisées 168.63.129.16 et 169.254.169.254. Ces adresses IP appartiennent à Microsoft et sont les seules adresses IP virtualisées utilisées à cet effet dans toutes les régions.
- **Gestion des licences (Service de gestion des clés)** : Les images Windows en cours d’exécution sur les machines virtuelles doivent être acquises sous licence. Pour assurer la gestion des licences, une requête est envoyée aux serveurs hôtes du Service de gestion de clés qui gèrent les requêtes de ce type. La requête est effectuée en sortie par le biais du port 1688. Cette règle de plateforme est désactivée pour les déploiements utilisant la configuration [itinéraire par défaut 0.0.0.0/0](virtual-networks-udr-overview.md#default-route).
- **Machines virtuelles dans des pools d’équilibrage de charge** : Le port source et la plage d’adresses appliquées proviennent de l’ordinateur d’origine, pas de l’équilibreur de charge. La plage de ports et d’adresses de destination sont ceux de l’ordinateur de destination, et non de l’équilibreur de charge.
- **Instances de service Azure** : Les instances de plusieurs services Azure, tels que HDInsight, les environnements de service d’application et Virtual Machine Scale Sets, sont déployées dans des sous-réseaux du réseau virtuel. Pour obtenir la liste complète des services que vous pouvez déployer sur des réseaux virtuels, consultez [Réseau virtuel pour les services Azure](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network). Familiarisez-vous avec les exigences de port pour chaque service avant d’appliquer un groupe de sécurité réseau au sous-réseau dans lequel la ressource est déployée. Si vous refusez les ports requis par le service, ce dernier ne fonctionnera pas correctement.
- **Envoi d’e-mail sortant** : Microsoft vous recommande l’utilisation de services de relais authentifiés SMTP (généralement connectés via le port TCP 587, mais parfois via d’autres ports) pour envoyer un e-mail depuis des machines virtuelles Azure. Les services de relais SMTP se spécialisent dans la réputation des expéditeurs, afin de minimiser les risques de renvoi de messages de la part de fournisseurs de messagerie tiers. Ce type de services de relais SMTP incluent, sans s’y limiter, Exchange Online Protection et SendGrid. L’utilisation de services de relais SMTP n’est en aucun cas limitée dans Azure et ne tient pas compte de votre type d’abonnement. 

  Si vous avez créé votre abonnement Azure avant le 15 novembre 2017, vous pouvez, en plus d’utiliser des services de relais SMTP, envoyer des messages électroniques via le port TCP 25 directement. Si vous avez créé votre abonnement après le 15 novembre 2017, vous ne serez peut-être pas en mesure d’envoyer des messages électroniques via le port TCP 25 directement. Le comportement des communications sortantes via le port 25 dépend de votre type d’abonnement :

     - **Contrat Entreprise** : Les communications sortantes via le port 25 sont autorisées. Vous êtes en mesure d’envoyer du courrier sortant directement depuis les machines virtuelles vers des fournisseurs de messagerie électronique externes, sans limitations de la plateforme Azure. 
     - **Paiement à l’utilisation** : Les communications sortantes via le port 25 sont bloquées pour toutes les ressources. Si vous devez envoyer des courriers électroniques directement depuis une machine virtuelle vers des fournisseurs de messagerie électronique externes (sans utiliser des relais SMTP authentifiés), vous pouvez effectuer une requête pour retirer la restriction. Les demandes sont étudiées et acceptées par Microsoft. Elles ne sont accordées qu’après des vérifications antifraude. Pour effectuer une requête, ouvrez un cas d’assistance avec pour type de problème *Technique*, *Connectivité du réseau virtuel*, *Envoi de messages électroniques impossible (SMTP/Port 25)*. Dans votre cas d’assistance, indiquez les raisons pour lesquelles votre abonnement doit être en mesure d’envoyer des courriers électroniques directement aux fournisseurs, sans passer par un relais authentifié SMTP. Si votre abonnement est exempté, seules les machines virtuelles créées après la date d’exemption sont en mesure d’utiliser des communications sortantes via le port 25.
     - **MSDN, Pass Azure, Azure dans Open, Éducation, BizSpark et Essai gratuit** : Les communications sortantes via le port 25 sont bloquées pour toutes les ressources. Aucune demande pour retirer la restriction ne peut être faite. Elles ne sont pas autorisées. Si vous devez envoyer des courriers électroniques depuis votre machine virtuelle, vous devez utiliser un service de relais SMTP.
     - **Fournisseur de services cloud** : Les clients qui consomment des ressources Azure via un fournisseur de services cloud peuvent créer une demande de support auprès de celui-ci et demander qu’il crée une demande de déblocage en son nom, si un relais SMTP sécurisé ne peut pas être utilisé.

  Si Azure vous permet d’envoyer des courriers électroniques via le port 25, Microsoft ne peut vous garantir que les fournisseurs de messagerie électronique accepteront le message entrant en provenance de votre machine virtuelle. Si un fournisseur spécifique rejette les messages en provenance de votre machine virtuelle, contactez directement le fournisseur pour résoudre tout problème de livraison de messages ou de filtrage de spam, ou bien utilisez un service de relais SMTP authentifié.

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [créer des groupes de sécurité réseau](tutorial-filter-network-traffic.md).
