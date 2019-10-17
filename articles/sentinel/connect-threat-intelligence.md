---
title: Connecter des données d’intelligence des menaces à Azure Sentinel | Microsoft Docs
description: Découvrez comment connecter des données de veille contre les menaces à Azure Sentinel.
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/24/2019
ms.author: rkarlin
ms.openlocfilehash: 44b3830465bf2b5aa06612aa868b086b120f1ece
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72372276"
---
# <a name="connect-data-from-threat-intelligence-providers"></a>Connecter des données issues de fournisseurs de veille contre les menaces

> [!IMPORTANT]
> Le connecteur de données Plateformes Threat Intelligence dans Azure Sentinel est actuellement en préversion publique.
> Cette fonctionnalité est fournie sans contrat de niveau de service et n’est pas recommandée pour des charges de travail en production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Sentinel vous permet d’importer les indicateurs de menace que votre organisation utilise, ce qui peut améliorer la capacité de vos analystes de sécurité à détecter et à hiérarchiser des menaces connues. Plusieurs fonctionnalités d’Azure Sentinel deviennent ensuite disponibles ou sont améliorées :

- L’**analytique** comprend un ensemble de modèles de règles planifiées que vous pouvez activer pour générer des alertes et incidents basés sur des correspondances d’événements de journal de vos indicateurs de menace.

- Les **classeurs** fournissent des informations résumées sur les indicateurs de menace importés dans Azure Sentinel et toutes les alertes générées à partir des règles d’analyse correspondant à vos indicateurs de menace.

- Les requêtes de **chasse** permettent aux investigateurs de sécurité d’utiliser des indicateurs de menace dans le cadre de scénarios de chasse courants.

- Les **notebooks** peuvent utiliser des indicateurs de menace lorsque vous enquêtez sur des anomalies et recherchez des comportements malveillants.

Vous pouvez diffuser en continu des indicateurs de menace vers Azure Sentinel en utilisant l’un des produits de la plateforme Threat Intelligence intégrée répertoriés dans la section suivante, ou en utilisant une intégration directe avec l’[API Microsoft Graph Security tiIndicators](https://aka.ms/graphsecuritytiindicators).

## <a name="integrated-threat-intelligence-platform-products"></a>Produits de la plateforme Threat Intelligence intégrée

- [Plateforme Threat Intelligence open source MISP](https://www.misp-project.org/)
    
    Pour obtenir un exemple de script qui fournit aux clients des instances MISP pour migrer les indicateurs de menace vers l’API Microsoft Graph Security, voir [MISP pour le script Microsoft Graph Security](https://github.com/microsoftgraph/security-api-solutions/tree/master/Samples/MISP).

- [Palo Alto Networks MineMeld](https://www.paloaltonetworks.com/products/secure-the-network/subscriptions/minemeld)
    
    Pour obtenir des instructions pas à pas, voir [Envoi d’IOCs à l’API Microsoft Graph Security à l’aide de MineMeld.](https://live.paloaltonetworks.com/t5/MineMeld-Articles/Sending-IOCs-to-the-Microsoft-Graph-Security-API-using-MineMeld/ta-p/258540)

- [Plateforme ThreatConnect](https://threatconnect.com/solution/)


## <a name="prerequisites"></a>Prérequis  

- Rôle Azure AD d’administrateur général ou d’administrateur de la sécurité pour accorder des autorisations à votre produit ou application personnalisée de plateforme Threat Intelligence qui utilise une intégration directe avec l’API Microsoft Graph Security tiIndicators.

- Autorisations en lecture et en écriture dans l’espace de travail Azure Sentinel pour stocker vos indicateurs de menace.

## <a name="connect-azure-sentinel-to-your-threat-intelligence-provider"></a>Connecter Azure Sentinel à votre fournisseur d’intelligence des menaces

1. [Inscrivez une application](/graph/auth-v2-service#1-register-your-app) dans Azure Active Directory pour recevoir un ID d’application, une clé secrète d’application et un ID de locataire Azure Active Directory. Vous avez besoin de ces valeurs au moment de configurer votre produit ou application intégrés de la plateforme Threat Intelligence qui utilise l’intégration directe avec l’API Microsoft Graph Security tiIndicators.

2. [Configurez des autorisations d’API](/graph/auth-v2-service#2-configure-permissions-for-microsoft-graph) pour l’application inscrite : Ajoutez l’autorisation d’Application Microsoft Graph **ThreatIndicators.ReadWrite.OwnedBy** à votre application inscrite.

3. Demandez à votre administrateur client Azure Active Directory d’accorder un consentement administrateur à l’application inscrite pour votre organisation. À partir du portail Azure : **Azure Active Directory** > **Inscriptions des applications** >  **\<_Nom de l’application_>**  > **Afficher les autorisations de l’API** > **Accorder un consentement administrateur pour \<_nom du locataire_>** .

4. Configurez votre produit ou application de la plateforme Threat Intelligence qui utilise une intégration directe avec l’API Microsoft Graph Security tiIndicators pour envoyer des indicateurs à Azure Sentinel en spécifiant les éléments suivants :
    
    a. Valeurs pour l’ID, le secret et l’ID de locataire de l’application inscrite.
    
    b. Pour le produit cible, spécifiez Azure Sentinel.
    
    c. Pour l’action, spécifiez alerte.

5. Dans le portail Azure, accédez à **Azure Sentinel** > **Connecteurs de données**, puis sélectionnez le connecteur **Plateformes Threat Intelligence (préversion)** .

6. Sélectionnez **Ouvrir la page du connecteur**, puis **Connecter**.

7. Pour afficher les indicateurs de menace importés dans Azure Sentinel, accédez à **Azure Sentinel - Logs** > **SecurityInsights**, puis développez **ThreatIntelligenceIndicator**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez appris à connecter votre fournisseur d’intelligence des menaces à Azure Sentinel. Pour en savoir plus sur Azure Sentinel, consultez les articles suivants.

- Découvrez comment [avoir une visibilité sur vos données et les menaces potentielles](quickstart-get-visibility.md).
- Prise en main de la [détection des menaces avec Azure Sentinel](tutorial-detect-threats.md).
