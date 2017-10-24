---
title: "Mettre automatiquement à l’échelle les unités de débit Azure Event Hubs | Microsoft Docs"
description: "Activer la majoration automatique sur un espace de noms pour mettre automatiquement à l’échelle les unités de débit"
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2017
ms.author: sethm
ms.openlocfilehash: 1cd31e0866ee6088483f88e8f80d01f75764c771
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="automatically-scale-up-azure-event-hubs-throughput-units"></a>Mettre automatiquement à l’échelle les unités de débit Azure Event Hubs

Azure Event Hubs est une plateforme hautement évolutive de diffusion de données en continu. Par conséquent, les clients Event Hubs augmentent souvent leur utilisation après leur intégration au service. Ces augmentations obligent à rehausser les unités de débit prédéterminées pour mettre à l’échelle Event Hubs et gérer des taux de transfert plus conséquents. La fonctionnalité de *majoration automatique* (Auto-inflate) d’Event Hubs effectue automatiquement une montée en puissance en augmentant le nombre d’unités de débit pour répondre aux besoins d’utilisation. Cette opération permet d’éviter les situations de limitation, dans lesquelles :

* Les taux d’entrée de données sont supérieurs aux unités de débit définies.
* Les taux de demande de sortie de données sont supérieurs aux unités de débit définies.

## <a name="how-auto-inflate-works"></a>Fonctionnement de la majoration automatique

Le trafic Event Hubs est contrôlé par les unités de débit. Une unité de débit autorise 1 Mo/s en entrée et le double en sortie. Les concentrateurs d’événements Standard peuvent être configurés avec un nombre d’unités de débit compris entre 1 et 20. La majoration automatique vous permet de démarrer avec le nombre d’unités de débit minimal requis. Ensuite, la fonctionnalité met automatiquement à l’échelle le nombre d’unités de débit dont vous avez besoin sur la limite maximale, selon l’augmentation de votre trafic. La majoration automatique vous permet de bénéficier des avantages suivants :

- Un mécanisme de mise à l’échelle efficace pour démarrer avec la valeur minimale et monter en puissance à mesure de la croissance de votre activité.
- Mise à l’échelle automatique sur à la limite supérieure spécifiée sans problèmes de limitation.
- Contrôle amélioré de la mise à l’échelle, car vous contrôlez le moment et la quantité de la mise à l’échelle.

## <a name="enable-auto-inflate-on-a-namespace"></a>Activer la majoration automatique sur un espace de noms

Vous pouvez activer ou désactiver la majoration automatique sur un espace de noms avec l’une des méthodes suivantes :

1. Le [portail Azure](https://portal.azure.com).
2. Un modèle Azure Resource Manager.

### <a name="enable-auto-inflate-through-the-portal"></a>Activer la majoration automatique via le portail

Vous pouvez activer la fonctionnalité de majoration automatique sur un espace de noms lors de la création d’un espace de noms Event Hubs :
 
![](./media/event-hubs-auto-inflate/event-hubs-auto-inflate1.png)

Une fois cette option activée, vous pouvez commencer par utiliser le nombre minimal d’unités de débit, puis monter en puissance à mesure que vos besoins d’utilisation augmentent. La limite supérieure de la majoration n’affecte pas les prix, qui dépendent du nombre d’unités de débit utilisées par heure.

Vous pouvez également activer la majoration automatique à l’aide de l’option **Mettre à l’échelle** dans le panneau des paramètres du portail :
 
![](./media/event-hubs-auto-inflate/event-hubs-auto-inflate2.png)

### <a name="enable-auto-inflate-using-an-azure-resource-manager-template"></a>Activer la majoration automatique à l’aide d’un modèle Azure Resource Manager

Vous pouvez activer la majoration automatique durant le déploiement d’un modèle Azure Resource Manager. Par exemple, définissez la propriété `isAutoInflateEnabled` sur **true** et définissez `maximumThroughputUnits` sur 10.

```json
"resources": [
        {
            "apiVersion": "2017-04-01",
            "name": "[parameters('namespaceName')]",
            "type": "Microsoft.EventHub/Namespaces",
            "location": "[variables('location')]",
            "sku": {
                "name": "Standard",
                "tier": "Standard"
            },
            "properties": {
                "isAutoInflateEnabled": true,
                "maximumThroughputUnits": 10
            },
            "resources": [
                {
                    "apiVersion": "2017-04-01",
                    "name": "[parameters('eventHubName')]",
                    "type": "EventHubs",
                    "dependsOn": [
                        "[concat('Microsoft.EventHub/namespaces/', parameters('namespaceName'))]"
                    ],
                    "properties": {},
                    "resources": [
                        {
                            "apiVersion": "2017-04-01",
                            "name": "[parameters('consumerGroupName')]",
                            "type": "ConsumerGroups",
                            "dependsOn": [
                                "[parameters('eventHubName')]"
                            ],
                            "properties": {}
                        }
                    ]
                }
            ]
        }
    ]
```

Pour accéder au modèle complet, consultez le modèle [Create Event Hubs namespace and enable inflate](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-inflate) (Créer un espace de noms Event Hubs et activer la majoration) sur GitHub.

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez en apprendre plus sur Event Hubs en consultant les liens suivants :

* [Vue d’ensemble des hubs d’événements](event-hubs-what-is-event-hubs.md)
* [Créer un concentrateur d’événements](event-hubs-create.md)
