---
title: Exécuter un service Azure Service Fabric comme utilisateur ou groupe AD | Microsoft Docs
description: Découvrez comment exécuter un service comme utilisateur ou groupe Active Directory sur un cluster autonome Service Fabric Windows.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/29/2018
ms.author: dekapur
ms.openlocfilehash: 3e0bb62609f13430bd2beab2332a31983874eb8e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60837723"
---
# <a name="run-a-service-as-an-active-directory-user-or-group"></a>Exécuter un service comme utilisateur ou groupe Active Directory
Sur un cluster autonome Windows Server, vous pouvez exécuter un service comme utilisateur ou groupe Active Directory avec une stratégie RunAs.  Par défaut, les applications Service Fabric s’exécutent sous le compte qui exécute le processus Fabric.exe. Les applications en cours d’exécution sous différents comptes sont plus sécurisées, même dans un environnement hébergé partagé. Remarque : cette fonctionnalité utilise Active Directory en local au sein de votre domaine et non Azure Active Directory (Azure AD).  Vous pouvez également exécuter un service en tant que [compte de service administré de groupe (gMSA)](service-fabric-run-service-as-gmsa.md).

En utilisant un groupe ou un utilisateur de domaine, vous pouvez accéder à d’autres ressources dans le domaine (par exemple, les partages de fichiers) qui disposent d’autorisations.

L’exemple suivant montre un utilisateur Active Directory appelé *TestUser* avec un mot de passe de domaine chiffré à l’aide d’un certificat nommé *MyCert*. Vous pouvez utiliser la commande PowerShell `Invoke-ServiceFabricEncryptText` pour créer le texte de chiffrement du secret. Consultez [Gestion des secrets dans des applications Service Fabric](service-fabric-application-secret-management.md) pour en savoir plus.

Vous devez déployer la clé privée du certificat pour déchiffrer le mot de passe sur l’ordinateur local à l’aide d’une méthode hors bande (avec Azure Resource Manager dans Azure). Ensuite, lorsque Service Fabric déploie le package de service sur l’ordinateur, il est en mesure de déchiffrer le secret (ainsi que le nom d’utilisateur) et de s’authentifier auprès d’Active Directory pour s’exécuter sous ces informations d’identification.

```xml
<Principals>
  <Users>
    <User Name="TestUser" AccountType="DomainUser" AccountName="Domain\User" Password="[Put encrypted password here using MyCert certificate]" PasswordEncrypted="true" />
  </Users>
</Principals>
<Policies>
  <DefaultRunAsPolicy UserRef="TestUser" />
  <SecurityAccessPolicies>
    <SecurityAccessPolicy ResourceRef="MyCert" PrincipalRef="TestUser" GrantRights="Full" ResourceType="Certificate" />
  </SecurityAccessPolicies>
</Policies>
<Certificates>
```

> [!NOTE] 
> Si vous appliquez une stratégie Run-As à un service et que le manifeste de service déclare des ressources de point de terminaison avec le protocole HTTP, vous devez également spécifier une **SecurityAccessPolicy**.  Pour plus d’informations, consultez [Assigner une stratégie d’accès de sécurité pour des points de terminaison HTTP et HTTPS](service-fabric-assign-policy-to-endpoint.md). 
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
Comme prochaine étape, lisez les articles suivants :
* [Comprendre le modèle d'application](service-fabric-application-model.md)
* [Spécifier des ressources dans un manifeste de service](service-fabric-service-manifest-resources.md)
* [Déployer une application](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
