---
title: Affectation de stratégies d’accès à des points de terminaison du service Azure Service Fabric | Microsoft Docs
description: Découvrez comment attribuer des stratégies d’accès de sécurité à des points de terminaison HTTP ou HTTPS dans votre service Service Fabric.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: ''
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/21/2018
ms.author: mfussell
ms.openlocfilehash: 8d5017cbd2177d080e5cef3d99a9f6b62eae08d5
ms.sourcegitcommit: 1fc949dab883453ac960e02d882e613806fabe6f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2018
ms.locfileid: "50978959"
---
# <a name="assign-a-security-access-policy-for-http-and-https-endpoints"></a>Affectation d’une stratégie d’accès de sécurité pour des points de terminaison HTTP et HTTPS
Si vous appliquez une stratégie RunAs et que le manifeste de service déclare des ressources de point de terminaison HTTP, vous devez spécifier une **SecurityAccessPolicy**.  **SecurityAccessPolicy** garantit que les ports affectés à ces points de terminaison sont correctement limités pour le compte utilisateur selon lequel le service s’exécute. Sinon, **http.sys** n’a pas accès au service et les appels en provenance du client échouent. L’exemple suivant applique le compte Customer1 au point de terminaison **EndpointName**, ce qui lui attribue des droits d’accès complets.

```xml
<Policies>
  <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
  <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
  <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
</Policies>
```

Pour un point de terminaison HTTPS, vous devez également indiquer le nom du certificat à retourner au client. Vous référencez le certificat à l’aide de **EndpointBindingPolicy**.  Le certificat est défini dans la section **Certificats** du manifeste de l’application.

```xml
<Policies>
  <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
  <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
  <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
  <!--EndpointBindingPolicy is needed if the EndpointName is secured with https -->
  <EndpointBindingPolicy EndpointRef="EndpointName" CertificateRef="Cert1" />
</Policies>
```

> [!WARNING] 
> Lorsque vous utilisez HTTPS, n’utilisez pas le même port ni le même certificat pour les différentes instances de service (indépendantes de l’application) déployées sur le même nœud. La mise à niveau de deux services différents utilisant le même port dans différentes instances d’application aboutit à un échec. Pour plus d’informations, consultez [Mise à niveau de plusieurs applications avec des points de terminaison HTTPS](service-fabric-application-upgrade.md#upgrading-multiple-applications-with-https-endpoints).
> 

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->Pour les prochaines étapes, lisez les articles suivants :
* [Comprendre le modèle d'application](service-fabric-application-model.md)
* [Spécifier des ressources dans un manifeste de service](service-fabric-service-manifest-resources.md)
* [Déployer une application](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
