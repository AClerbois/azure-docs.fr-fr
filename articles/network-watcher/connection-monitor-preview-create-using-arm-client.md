---
title: Créer un Moniteur de connexion en préversion - ARMClient
titleSuffix: Azure Network Watcher
description: Découvrez comment créer une instance Moniteur de connexion (préversion) à l’aide du ARMClient.
services: network-watcher
documentationcenter: na
author: vinigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/30/2020
ms.author: vinigam
ms.openlocfilehash: 7d35799cd73ff4d065cb58189f2325dc4dac6840
ms.sourcegitcommit: 97a0d868b9d36072ec5e872b3c77fa33b9ce7194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/04/2020
ms.locfileid: "87567801"
---
# <a name="create-a-connection-monitor-preview-using-the-armclient"></a>Créer une instance Moniteur de connexion (préversion) à l’aide du ARMClient

Découvrez comment créer une instance Moniteur de connexion (préversion) pour surveiller la communication entre vos ressources à l’aide du ARMClient. Elle prend en charge les déploiements cloud hybrides et Azure.

## <a name="before-you-begin"></a>Avant de commencer 

Dans les moniteurs de connexion que vous créez à partir de la fonctionnalité Moniteur de connexion (préversion), vous pouvez aussi bien ajouter des machines locales et des machines virtuelles Azure en tant que sources. Ces moniteurs de connexion peuvent également surveiller la connectivité aux points de terminaison. Les points de terminaison peuvent se trouver sur Azure ou sur toute autre URL ou adresse IP.

Le Moniteur de connexion (préversion) inclut les entités suivantes :

* **Ressource de moniteur de connexion** : ressource Azure spécifique à la région. Toutes les entités ci-dessous sont des propriétés d'une ressource de moniteur de connexion.
* **Point de terminaison** : source ou destination qui participe aux vérifications de la connectivité. Les machines virtuelles Azure, les agents locaux, les URL et les adresses IP sont des exemples de points de terminaison.
* **Configuration de test** : configuration spécifique à un protocole dans le cadre d'un test. En fonction du protocole que vous avez choisi, vous pouvez définir le port, les seuils, la fréquence de test et d'autres paramètres.
* **Groupe de tests** : groupe contenant les points de terminaison sources, les points de terminaison de destination et les configurations de test. Un moniteur de connexion peut contenir plusieurs groupes de tests.
* **Test** : combinaison d'un point de terminaison source, d'un point de terminaison de destination et d'une configuration de test. Un test correspond au niveau le plus granulaire auquel les données de surveillance sont disponibles. Les données de surveillance incluent le pourcentage de vérifications qui ont échoué et la durée des boucles.

    ![Diagramme illustrant un moniteur de connexion, avec définition de la relation entre les groupes de tests et les tests](./media/connection-monitor-2-preview/cm-tg-2.png)

## <a name="steps-to-create-with-sample-arm-template"></a>Étapes pour créer avec l’exemple de modèle ARM

Utilisez le code suivant pour créer un moniteur de connexion à l'aide d'ARMClient.

```armclient
$connectionMonitorName = "sampleConnectionMonitor"

$ARM = "https://management.azure.com"

$SUB = "subscriptions/<subscription id 1>;"

$NW = "resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher\_<region>"

$body =

"{

location: '<region>',

properties: {

endpoints: [{

name: 'workspace',

resourceId: '/subscriptions/<subscription id>/resourcegroups/<resource group>/providers/Microsoft.OperationalInsights/workspaces/sampleWorkspace',

filter: {

 items: [{

type: 'AgentAddress',

address: '<FQDN of your on-premises agent>'

}]

}

          },

 {

name: 'vm1',

resourceId: '/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<vm-name>'

},

 {

name: 'vm2',

resourceId: '/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<vm-name>'

   },

{

name: 'azure portal'

address: '<URL>'

   },

 {

    name: 'ip',

     address: '<IP>'

 }

  ],

  testGroups: [{

    name: 'Connectivity to Azure Portal and Public IP',

    testConfigurations: ['http', 'https', 'tcpEnabled', 'icmpEnabled'],

    sources: ['vm1', 'workspace'],

    destinations: ['azure portal', 'ip']

   },

{

    name: 'Connectivty from Azure VM 1 to Azure VM 2',

   // Choose your protocol
   
    testConfigurations: ['http', 'https', 'tcpDisabled', 'icmpDisabled'],

    sources: ['vm1'],

    destinations: ['vm2'],

    disable: true

   }

  ],

  testConfigurations: [{

    name: 'http',

    testFrequencySec: <frequency>,

    protocol: 'HTTP',

    successThreshold: {

     checksFailedPercent: <threshold for checks failed %>,

     roundTripTimeMs: <threshold for RTT>

    }

   }, {

    name: 'https',

    testFrequencySec: <frequency>,

    protocol: 'HTTP',

    httpConfiguration: {

     preferHTTPS: true

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'tcpEnabled',

    testFrequencySec: <frequency>,

    protocol: 'TCP',

    tcpConfiguration: {

     port: 80

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'icmpEnabled',

    testFrequencySec: <frequency>,

    protocol: 'ICMP',

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'icmpDisabled',

    testFrequencySec: <frequency>,

    protocol: 'ICMP',

    icmpConfiguration: {

     disableTraceRoute: true

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'tcpDisabled',

    testFrequencySec: <frequency>,

    protocol: 'TCP',

    tcpConfiguration: {

     port: 80,

     disableTraceRoute: true

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }

  ]

 }

} "
```

La commande de déploiement est la suivante :
```
armclient PUT $ARM/$SUB/$NW/connectionMonitors/$connectionMonitorName/?api-version=2019-07-01 $body -verbose
```

## <a name="description-of-properties"></a>Description des propriétés

* connectionMonitorName - Nom de la ressource du moniteur de connexion

* SUB - ID de l’abonnement dans lequel vous souhaitez créer un moniteur de connexion (CM)

* NW - ID de ressource Network Watcher dans lequel CM sera créé 

* location - Région dans laquelle le moniteur de connexion sera créé

* Points de terminaison
    * name - Nom unique de chaque point de terminaison
    * resourceId - Pour les points de terminaison Azure, l’ID de ressource fait référence à l’ID de ressource Azure Resource Manager (ARM) pour les ordinateurs virtuels. Pour les points de terminaison non-Azure, l’ID de ressource fait référence à l’ID de ressource ARM pour l’espace de travail Log Analytics lié aux agents non-Azure.
    * adress - Applicable uniquement lorsque l’ID de ressource n’est pas spécifié ou si l’ID de ressource est l’espace de travail Log Analytics. Si utilisée avec l’ID de ressource Log Analytics, fait référence au nom de domaine complet de l’agent qui peut être utilisé pour l’analyse. Si utilisée sans ID de ressource, il peut s’agir de l’URL ou de l’adresse IP d’un point de terminaison public.
    * filter - Pour les points de terminaison non-Azure, utilisez filter pour sélectionner des agents dans l’espace de travail Log Analytics qui sera utilisé pour l’analyse dans la ressource du moniteur de connexion. Si les filtres ne sont pas définis, tous les agents appartenant à l’espace de travail Log Analytics peuvent être utilisés pour l’analyse.
        * type – Définissez le type comme « Agent Adress » (Adresse de l’agent).
        * adress – Définissez l’adresse comme nom de domaine complet de votre agent local.

* Groupes de test
    * name - Donnez un nom à votre groupe de test.
    * testConfigurations - Configurations de test basées sur les points de terminaison sources qui se connectent aux points de terminaison de destination.
    * sources - Choisissez parmi les points de terminaison créés ci-dessus. Les points de terminaison sources basés sur Azure doivent être dotés de l’extension Azure Network Watcher, et les points de terminaison sources non basés sur Azure doivent avoir l’agent Azure Log Analytics installé. Pour installer un agent pour votre source, consultez [Installer des agents de surveillance](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#install-monitoring-agents).
    * destinations - Choisissez parmi les points de terminaison créés ci-dessus. Vous pouvez surveiller la connectivité aux machines virtuelles Azure ou aux points de terminaison (adresses IP publiques, URL ou FQDN) en les spécifiant en tant que destinations. Au sein d'un même groupe de tests, vous pouvez ajouter des machines virtuelles Azure, des URL Office 365, des URL Dynamics 365 et des points de terminaison personnalisés.
    * disable - Utilisez ce champ pour désactiver la surveillance de toutes les sources et destinations spécifiées par le groupe de tests.

* Configurations de test
    * name - Nom de la configuration de test.
    * testFrequencySec - Choisissez la fréquence à laquelle les sources effectueront un test Ping des destinations à l’aide du protocole et du port que vous avez spécifiés. Vous pouvez choisir 30 secondes, 1 minute, 5 minutes, 15 minutes ou 30 minutes. Les sources testeront la connectivité aux destinations en fonction de la valeur que vous avez choisie. Par exemple, si vous sélectionnez 30 secondes, les sources vérifieront la connectivité à la destination au moins une fois toutes les 30 secondes.
    * protocol - Vous pouvez choisir entre TCP, ICMP, HTTP ou HTTPS. Selon le protocole, vous pouvez effectuer des configurations spécifiques au protocole.
        * preferHTTPS - Spécifiez s’il faut utiliser HTTPS plutôt que HTTP.
        * port - Spécifiez le port de destination de votre choix.
        * disableTraceRoute - Ce champ s’applique aux groupes de tests utilisant le protocole TCP ou ICMP. Cela empêcher les sources de découvrir la topologie et la durée des boucles tronçon par tronçon.
    * successThreshold - Vous pouvez définir des seuils sur les paramètres réseau suivants :
        * checksFailedPercent - Définissez le pourcentage de vérifications qui peuvent échouer lorsque les sources vérifient la connectivité aux destinations à l’aide des critères que vous avez spécifiés. Pour le protocole TCP ou ICMP, le pourcentage de vérifications qui ont échoué peut être égal au pourcentage de perte de paquets. Pour le protocole HTTP, ce champ représente le pourcentage de requêtes HTTP qui n'ont reçu aucune réponse.
        * roundTripTimeMs - Définissez la durée des boucles en millisecondes pour déterminer combien de temps les sources peuvent prendre pour se connecter à la destination sur la configuration de test.

## <a name="scale-limits"></a>Limites de mise à l’échelle

Les moniteurs de connexion présentent les limites suivantes en termes de mise à l'échelle :

* Nombre maximum de moniteurs de connexion par abonnement et par région : 100
* Nombre maximum de groupes de tests par moniteur de connexion : 20
* Nombre maximum de sources et de destinations par moniteur de connexion : 100
* Nombre maximum de configurations de test par moniteur de connexion : 20 via ARMClient

## <a name="next-steps"></a>Étapes suivantes

* Découvrez [comment analyser les données de surveillance et définir des alertes](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#analyze-monitoring-data-and-set-alerts).
* Découvrez [comment diagnostiquer des problèmes dans votre réseau](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#diagnose-issues-in-your-network).
