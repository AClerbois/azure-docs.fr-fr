---
title: Fonctionnalités Azure Security Center pour les ressources Azure PaaS prises en charge.
description: Cette page indique la disponibilité des fonctionnalités Azure Security Center pour les ressources Azure PaaS prises en charge.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/01/2020
ms.author: memildin
ms.openlocfilehash: 7087fb7b1de0b16480d0bb02043b40e0e97204f6
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91449959"
---
# <a name="feature-coverage-for-azure-paas-services"></a>Couverture des fonctionnalités pour les services Azure PaaS <a name="paas-services"></a>

Le tableau ci-dessous indique la disponibilité des fonctionnalités Azure Security Center pour les ressources Azure PaaS prises en charge.

|Service|Recommandations (Gratuit)|Alertes de sécurité (Azure Defender)|Évaluation des vulnérabilités (Azure Defender)|
|:----|:----:|:----:|:----:|
|Azure App Service|✔|✔|-|
|Compte Azure Automation|✔|-|-|
|Compte Azure Batch|✔|-|-|
|Stockage Blob Azure|✔|✔|-|
|Cache Azure pour Redis|✔|-|-|
|Services cloud Azure|✔|-|-|
|Recherche cognitive Azure|✔|-|-|
|Azure Container Registry|-|-|✔|
|Azure Cosmos DB*|-|✔|-|
|Service Analytique Azure Data Lake|✔|-|-|
|Azure Data Lake Storage|✔|-|-|
|Azure Database pour MySQL*|✔|✔|-|
|Azure Database pour PostgreSQL*|✔|✔|-|
|Espace de noms Azure Event Hubs|✔|-|-|
|Application Azure Functions|✔|-|-|
|Azure Key Vault|✔|✔|-|
|Azure Kubernetes Service|✔|✔|-|
|Azure Load Balancer|✔|-|-|
|Azure Logic Apps|✔|-|-|
|Azure SQL Database|✔|✔|✔|
|Azure SQL Managed Instance|✔|✔|✔|
|Espace de noms Azure Service Bus|✔|-|-|
|Compte Azure Service Fabric|✔|-|-|
|Comptes de stockage Azure|✔|-|-|
|Azure Stream Analytics|✔|-|-|
|Abonnement Azure|✔ **|✔|-|
|Réseau virtuel Azure</br> (y compris les sous-réseaux, les cartes réseau et les groupes de sécurité réseau)|✔|-|-|

\* Ces fonctionnalités sont actuellement prises en charge en préversion.

\*\* Les recommandations Azure Active Directory (Azure AD) sont uniquement disponibles pour les abonnements avec Azure Defender activé.