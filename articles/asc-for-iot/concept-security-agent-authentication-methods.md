---
title: Méthodes d’authentification pour Azure Security Center pour IoT | Microsoft Docs
description: Découvrez les différentes méthodes d’authentification disponibles avec le service Azure Security Center pour l’IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 10b38f20-b755-48cc-8a88-69828c17a108
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 16f7f91e02d118d9f9a295ebb79a6cd0187dd9fd
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596472"
---
# <a name="security-agent-authentication-methods"></a>Méthodes d'authentification de l’agent de sécurité 

Cet article décrit les différentes méthodes d’authentification utilisables avec l’agent AzureIoTSecurity pour s’authentifier auprès d’IoT Hub.

Un module de sécurité est nécessaire pour chaque appareil intégré à Azure Security Center pour IoT dans IoT Hub. Pour authentifier l’appareil, Azure Security Center pour IoT peut utiliser deux méthodes. Choisissez la plus adaptée à votre solution IoT actuelle. 

> [!div class="checklist"]
> * Option SecurityModule
> * Option Appareil

## <a name="authentication-methods"></a>Méthodes d’authentification

Il existe deux méthodes permettant à l’agent AzureIoTSecurity d’effectuer l’authentification :

 - Mode d’authentification **SecurityModule**<br>
   L’agent est authentifié à l’aide de l’identité du module de sécurité indépendamment de l’identité de l’appareil.
   Utilisez ce type d’authentification si vous souhaitez que l’agent de sécurité utilise une méthode d’authentification dédiée par le biais du module de sécurité (clé symétrique uniquement).
        
 - Mode d'authentification **Appareil**<br>
    Dans cette méthode, l’agent de sécurité s’authentifie tout d’abord avec l’identité d’appareil. Après l’authentification initiale, l’agent Azure Security Center pour IoT effectue un appel **REST** vers IoT Hub en utilisant l’API REST et les données d’authentification de l’appareil. L’agent Azure Security Center pour IoT demande ensuite à IoT Hub la méthode d’authentification du module de sécurité et les données. Lors de la dernière étape, l’agent Azure Security Center pour IoT effectue une authentification vis-à-vis du module A.
    
    Utilisez ce type d’authentification si vous souhaitez que l’agent de sécurité réutilise une méthode d’authentification d’appareil existante (certificat auto-signé ou clé symétrique). 

Pour connaître la configuration, voir [Paramètres d’installation de l’agent de sécurité](#security-agent-installation-parameters).
                                
## <a name="authentication-methods-known-limitations"></a>Limitations connues des méthodes d’authentification

- Le mode d’authentification **SecurityModule** prend uniquement en charge l’authentification par clé symétrique.
- Le certificat signé par l’autorité de certification n’est pas pris en charge par le mode d’authentification **Appareil**.  

## <a name="security-agent-installation-parameters"></a>Paramètres d’installation de l’agent de sécurité

Lors du [déploiement d’un agent de sécurité](how-to-deploy-agent.md), les informations d’authentification doivent être indiquées comme arguments,
documentés dans le tableau suivant.


|Nom du paramètre Linux | Nom du paramètre Windows | Paramètre de raccourci |Description|Options|
|---------------------|---------------|---------|---------------|---------------|
|authentication-identity|AuthenticationIdentity|aui|Identité d’authentification| **SecurityModule** ou **Appareil**|
|authentication-method|AuthenticationMethod|aum|Méthode d’authentification|**SymmetricKey** ou **SelfSignedCertificate**|
|file-path|FilePath|f|Chemin d’accès complet absolu du fichier contenant le certificat ou la clé symétrique| |
|host-name|HostName|hn|FQDN du hub IoT|Exemple : ContosoIotHub.azure-devices.net|
|device-id|deviceId|di|ID de périphérique|Exemple : MyDevice1|
|certificate-location-kind|CertificateLocationKind|cI|Emplacement de stockage des certificats|**LocalFile** ou **Store**|
|


Si vous utilisez le script d’installation de l’agent de sécurité, la configuration suivante est effectuée automatiquement. Pour changer manuellement l’authentification de l’agent de sécurité, modifiez le fichier de configuration. 

## <a name="change-authentication-method-after-deployment"></a>Changer de méthode d’authentification après le déploiement

Si l’agent de sécurité est déployé avec un script d’installation, un fichier de configuration est automatiquement créé.

Pour changer de méthode d’authentification après le déploiement, il faut modifier manuellement ce fichier.


### <a name="c-based-security-agent"></a>Agent de sécurité C#

Modifiez _Authentication.config_ avec les paramètres suivants :

```xml
<Authentication>
  <add key="deviceId" value=""/>
  <add key="gatewayHostname" value=""/>
  <add key="filePath" value=""/>
  <add key="type" value=""/>
  <add key="identity" value=""/>
  <add key="certificateLocationKind" value="" />
</Authentication>
```

### <a name="c-based-security-agent"></a>Agent de sécurité C

Modifiez _LocalConfiguration.json_ avec les paramètres suivants :

```json
"Authentication" : {
    "Identity" : "",
    "AuthenticationMethod" : "",
    "FilePath" : "",
    "DeviceId" : "",
    "HostName" : ""
}
```

## <a name="see-also"></a>Voir aussi
- [Vue d’ensemble des agents de sécurité](security-agent-architecture.md)
- [Déployer un agent de sécurité](how-to-deploy-agent.md)
- [Accéder aux données de sécurité brutes](how-to-security-data-access.md)
