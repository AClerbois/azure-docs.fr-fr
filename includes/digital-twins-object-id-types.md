---
title: Fichier Include
description: Fichier Include
services: digital-twins
author: kingdomofends
ms.service: digital-twins
ms.topic: include
ms.date: 12/20/2018
ms.author: adgera
ms.custom: include file
ms.openlocfilehash: 40ab53c941a7ac619ebb09d381a4ae0450f26e8b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66162095"
---
Le `objectIdType` (ou le **type d’identificateur d’objet**) fait référence au type d’identité auquel un rôle est attribué. En dehors des types `DeviceId` et `UserDefinedFunctionId`, les types d’identificateur d’objet correspondent à des propriétés d’objets Azure Active Directory.

Le tableau suivant contient les types d’identificateurs d’objet pris en charge dans Azure Digital Twins :

| Type | Description |
| --- | --- |
| UserId | Attribue un rôle à un utilisateur. |
| deviceId | Attribue un rôle à un appareil. |
| DomainName | Attribue un rôle à un nom de domaine. Chaque utilisateur avec le nom de domaine spécifié a les droits d’accès du rôle correspondant. |
| TenantId | Attribue un rôle à un locataire. Chaque utilisateur qui appartient à l’ID de locataire Azure AD spécifié a les droits d’accès du rôle correspondant. |
| ServicePrincipalId | Attribue un rôle à un ID d’objet de principal de service. |
| UserDefinedFunctionId | Attribue un rôle à une fonction définie par l’utilisateur. |