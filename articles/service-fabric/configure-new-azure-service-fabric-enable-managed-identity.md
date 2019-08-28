---
title: Azure Service Fabric - Déployer un nouveau cluster Azure Service Fabric avec une prise en charge de l’identité managée | Microsoft Docs
description: Cet article explique comment créer un nouveau cluster Service Fabric avec l’identité managée activée
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.topic: article
ms.date: 07/25/2019
ms.author: atsenthi
ms.openlocfilehash: cb32251c2507f3def7f5e830af15828ff2f04c29
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2019
ms.locfileid: "69640718"
---
# <a name="create-a-new-azure-service-fabric-cluster-with-managed-identity-support-preview"></a>Créer un nouveau cluster Azure Service Fabric avec une prise en charge d’identité managée (préversion)

Pour accéder à la fonctionnalité d’identité managée pour les applications Azure Service Fabric, vous devez d’abord activer le service de jeton d’identité managée sur le cluster. Ce service est responsable de l’authentification des applications Service Fabric à l’aide de leurs identités managées et de l’obtention de jetons d’accès en leur nom. Une fois le service activé, vous pouvez le voir dans Service Fabric Explorer sous la section **Système** dans le volet gauche, exécuté sous le nom **fabric:/System/ManagedIdentityTokenService** à côté d’autre services système.

> [!NOTE]
> Le runtime Service Fabric version 6.5.658.9590 ou une version ultérieure est requis pour activer le **service de jeton d’identité managée**.  

## <a name="enable-the-managed-identity-token-service"></a>Activer le service de jeton l’identité managée 
Pour activer le service de jeton d’identité managée au moment de la création du cluster, vous pouvez utiliser l’extrait de code suivant dans un modèle Azure Resource Manager :

```json
"fabricSettings": [
    {
        "name": "ManagedIdentityTokenService",
        "parameters": [
            {
                "name": "IsEnabled",
                "value": "true"
            }
        ]
    }
]
```

## <a name="errors"></a>Errors

Si le déploiement échoue avec ce message, cela signifie que le cluster n’est pas à la version de Service Fabric requise (le runtime minimal pris en charge est 6.5 CU2) :



```json
{
    "code": "ParameterNotAllowed",
    "message": "Section 'ManagedIdentityTokenService' and Parameter 'IsEnabled' is not allowed."
}
```


## <a name="related-articles"></a>Articles connexes
* Vérifier la [prise en charge des identités managées](./concepts-managed-identity.md) dans Azure Service Fabric

* [Activer la prise en charge d’identité managée dans un cluster Azure Service Fabric existant](./configure-existing-cluster-enable-managed-identity-token-service.md)

## <a name="next-steps"></a>Étapes suivantes
* [Déployer une application Azure Service Fabric avec une identité managée attribuée par le système](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Déployer une application Azure Service Fabric avec une identité managée attribuée par l’utilisateur](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Tirer parti de l’identité managée d’une application Service Fabric à partir du code de service](./how-to-managed-identity-service-fabric-app-code.md)
* [Accorder à une application Azure Service Fabric l’accès à d’autres ressources Azure](./how-to-grant-access-other-resources.md)