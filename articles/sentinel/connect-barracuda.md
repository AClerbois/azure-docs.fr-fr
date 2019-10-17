---
title: Connecter des données Barracuda à Azure Sentinel | Microsoft Docs
description: Découvrez comment connecter des données Barracuda à Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 3b33b4aa-7286-4d79-b461-8e1812edc2e1
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2019
ms.author: rkarlin
ms.openlocfilehash: 5142204db86158937c16d19bf52fb677099598a5
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2019
ms.locfileid: "72284664"
---
# <a name="connect-your-barracuda-appliance"></a>Connecter votre appliance Barracuda 



Le connecteur du pare-feu d’applications web (WAF) Barracuda vous permet de connecter facilement vos journaux Barracuda à Azure Sentinel, de consulter des tableaux de bord, de créer des alertes personnalisées et d’améliorer les enquêtes. Cela vous donne plus d’informations sur le réseau de votre organisation et améliore vos capacités d’opération de sécurité. Azure Sentinel tire parti de l'intégration native entre **Barracuda** et Log Analytics Agent pour fournir une intégration fluide. 


> [!NOTE]
> Les données seront stockées dans l’emplacement géographique de l’espace de travail sur lequel vous exécutez Azure Sentinel.

## <a name="configure-and-connect-barracuda-waf"></a>Configurer et connecter le pare-feu d’applications web Barracuda
Le pare-feu d'applications web Barracuda peut intégrer et exporter des journaux directement dans Azure Sentinel via Log Analytics Agent.
1. Accédez à [Flux de configuration du pare-feu d’applications web Barracuda](https://campus.barracuda.com/product/webapplicationfirewall/doc/73696965/configure-the-barracuda-web-application-firewall-to-integrate-with-the-oms-server-and-export-logs/) et suivez les instructions pour configurer la connexion à l’aide de ces paramètres :
    - **ID de l’espace de travail** : copiez la valeur de l’ID de votre espace de travail à partir de la page du connecteur Azure Sentinel Barracuda.
    - **Clé primaire** : copiez la valeur de votre clé primaire à partir de la page du connecteur Azure Sentinel Barracuda.
1. Pour utiliser le schéma approprié dans Log Analytics pour les événements Barracuda, recherchez **CommonSecurityLog** et **barracuda_CL**.


## <a name="validate-connectivity"></a>Valider la connectivité

Plus de 20 minutes peuvent être nécessaires avant que vos journaux ne commencent à apparaître dans Log Analytics. 



## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à connecter les appliances Barracuda à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :
- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](tutorial-detect-threats-built-in.md).

