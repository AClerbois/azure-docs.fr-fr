---
title: Configurer les paramètres de règle de détection intelligente Azure Application Insights avec les modèles Azure Resource Manager | Microsoft Docs
description: Automatiser la gestion et la configuration des règles de détection intelligente d’Azure Application Insights avec les modèles Azure Resource Manager
services: application-insights
documentationcenter: ''
author: harelbr
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/26/2019
ms.reviewer: mbullwin
ms.author: harelbr
ms.openlocfilehash: e7a54c2e207a27f3519375df09d0c930a92d52d6
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2019
ms.locfileid: "70193726"
---
# <a name="manage-application-insights-smart-detection-rules-using-azure-resource-manager-templates"></a>Gérer les règles de détection intelligente d’Application Insights à l’aide de modèles Azure Resource Manager

Les règles de détection intelligente dans Application Insights peuvent être gérées et configurées à l’aide de [modèles Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md).
Cette méthode peut être utilisée lors du déploiement de nouvelles ressources Application Insights avec Azure Resource Manager Automation, ou pour modifier les paramètres des ressources existantes.

## <a name="smart-detection-rule-configuration"></a>Configuration de règle de détection intelligente

Vous pouvez configurer les paramètres suivants pour une règle de détection intelligente :
- Si la règle est activée (la valeur par défaut est **true**.)
- Si les e-mails doivent être envoyés aux utilisateurs associés aux rôles [Lecteur de surveillance](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) et [Contributeur de surveillance](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) de l’abonnement à la suite d’une détection (la valeur par défaut est **true**).
- Les destinataires d’e-mails supplémentaires qui doivent recevoir une notification lorsqu’une détection est trouvée.
    -  La configuration relative aux e-mails n’est pas disponible pour les règles de détection intelligente indiquant _Préversion_.

Pour autoriser la configuration des paramètres de règle via Azure Resource Manager, la configuration de règle de détection intelligente est désormais disponible en tant que ressource interne au sein de la ressource Application Insights, nommée **ProactiveDetectionConfigs**.
Pour une flexibilité maximale, chaque règle de détection intelligente peut être configurée avec les paramètres de notification unique.

## 

## <a name="examples"></a>Exemples

Voici quelques exemples montrant comment configurer les paramètres des règles de détection intelligente à l’aide de modèles Azure Resource Manager.
Tous les exemples font référence à une ressource Application Insights nommée _« myApplication »_ , et à la « règle de détection intelligente de dépendance longue durée », qui est nommée en interne _« longdependencyduration »_ .
Assurez-vous de remplacer le nom de la ressource Application Insights et de spécifier le nom interne de règle de détection intelligente pertinente. Consultez le tableau ci-dessous pour obtenir la liste des noms Azure Resource Manager internes correspondants à chaque règle de détection intelligente.

### <a name="disable-a-smart-detection-rule"></a>Gérer des règles de détection intelligente

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "ApplicationId": "myApplication"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": true,
            "customEmails": [],
            "enabled": false
          }
        }
      ]
    }
```

### <a name="disable-sending-email-notifications-for-a-smart-detection-rule"></a>Désactiver l’envoi de notifications par e-mail pour une règle de détection intelligente

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "ApplicationId": "myApplication"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": false,
            "customEmails": [],
            "enabled": true
          }
        }
      ]
    }
```

### <a name="add-additional-email-recipients-for-a-smart-detection-rule"></a>Ajouter des destinataires d’e-mail supplémentaires pour une règle de détection intelligente

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "ApplicationId": "myApplication"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": true,
            "customEmails": ['alice@contoso.com', 'bob@contoso.com'],
            "enabled": true
          }
        }
      ]
    }

```

### <a name="failure-anomalies-v2-non-classic-alert-rule"></a>Règle d’alerte pour les défaillances v2 (non classique)

Ce modèle Azure Resource Manager montre comment configurer une règle d’alerte pour les défaillances v2 avec un niveau de gravité de 2. Cette nouvelle version de la règle d’alerte pour les défaillances fait partie de la nouvelle plateforme d’alertes d’Azure et remplace la version classique qui a été supprimée dans le cadre du [processus de suppression des alertes classiques](https://azure.microsoft.com/updates/classic-alerting-monitoring-retirement/).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "microsoft.alertsmanagement/smartdetectoralertrules",
            "apiVersion": "2019-03-01",
            "name": "Failure Anomalies - my-app",
            "location": "global", 
            "properties": {
                  "description": "Detects a spike in the failure rate of requests or dependencies",
                  "state": "Enabled",
                  "severity": "2",
                  "frequency": "PT1M",
                  "detector": {
                  "id": "FailureAnomaliesDetector"
                  },
                  "scope": ["/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/MyResourceGroup/providers/microsoft.insights/components/my-app"],
                  "actionGroups": {
                        "groupIds": ["/subscriptions/00000000-1111-2222-3333-444444444444/resourcegroups/MyResourceGroup/providers/microsoft.insights/actiongroups/MyActionGroup"]
                  }
            }
        }
    ]
}
```

> [!NOTE]
> Ce modèle Azure Resource Manager est spécifique à la règle d’alerte pour les défaillances v2 et il est différent des autres règles de détection intelligente classiques décrites dans cet article.   

## <a name="smart-detection-rule-names"></a>Règles de détection intelligente

Voici un tableau des noms de règle de détection intelligente tels qu’ils apparaissent dans le portail, ainsi que leurs noms internes, qui doit être utilisé dans le modèle Azure Resource Manager.

> [!NOTE]
> Les règles de détection intelligente indiquant _Préversion_ ne prennent pas en charge les notifications par e-mail. Par conséquent, vous pouvez uniquement définir la propriété _Activée_ pour ces règles. 

| Nom de la règle du portail Azure | Nom interne
|:---|:---|
| Durée de chargement de page lente | slowpageloadtime |
| Temps de réponse du serveur lent | slowserverresponsetime |
| Durée de dépendance longue | longdependencyduration |
| Dégradation du temps de réponse du serveur | degradationinserverresponsetime |
| Dégradation de la durée de dépendance | degradationindependencyduration |
| Dégradation du rapport entre les niveaux de gravité des suivis (préversion) | extension_traceseveritydetector |
| Élévation anormale du volume des exceptions (préversion) | extension_exceptionchangeextension |
| Fuite de mémoire potentielle détectée (préversion) | extension_memoryleakextension |
| Problème de sécurité potentiel détecté (préversion) | extension_securityextensionspackage |
| Élévation anormale du volume de données quotidien (préversion) | extension_billingdatavolumedailyspikeextension |

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur la détection automatique :

- [Défaillances](../../azure-monitor/app/proactive-failure-diagnostics.md)
- [Fuites de mémoire](../../azure-monitor/app/proactive-potential-memory-leak.md)
- [Anomalies de performance](../../azure-monitor/app/proactive-performance-diagnostics.md)
