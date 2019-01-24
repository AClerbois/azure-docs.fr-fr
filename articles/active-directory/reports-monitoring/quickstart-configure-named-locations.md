---
title: Configuration d’emplacements nommés dans Azure Active Directory | Microsoft Docs
description: Découvrez comment configurer des emplacements nommés.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
ms.assetid: f56e042a-78d5-4ea3-be33-94004f2a0fc3
ms.service: active-directory
ms.workload: identity
ms.component: report-monitor
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 45d3cebf9b410cb51106ac9255ff3ad461ea47d3
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54818045"
---
# <a name="quickstart-configure-named-locations-in-azure-active-directory"></a>Démarrage rapide : Configurer des emplacements nommés dans Azure Active Directory

Les emplacements nommés vous permettent d’étiqueter des plages d’adresses IP approuvées dans votre entreprise. Azure AD utilise des emplacements nommés pour effectuer les actions suivantes :
- Détecter les faux positifs dans les [événements à risque](concept-risk-events.md). La connexion à partir d’un emplacement approuvé réduit le risque de connexion d’un utilisateur.   
- Configurer l’[accès conditionnel en fonction des emplacements](../conditional-access/location-condition.md).

Dans ce guide de démarrage rapide, vous apprenez à configurer des emplacements nommés dans votre environnement.

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce démarrage rapide, les éléments suivants sont requis :

* Un locataire Azure AD. Inscrivez-vous pour obtenir un [essai gratuit](https://azure.microsoft.com/trial/get-started-active-directory/). 
* Un utilisateur, qui est administrateur général du locataire.
* Une plage IP qui est établie et fiable dans votre organisation. La plage d’adresses IP doit être au format **CIDR** (Classless Inter-Domain Routing).

## <a name="configure-named-locations"></a>Configurer des emplacements nommés

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

2. Dans le volet gauche, à partir de la section **Sécurité**, sélectionnez **Azure Active Directory**, puis **Accès conditionnel**.

    ![Onglet Accès conditionnel](./media/quickstart-configure-named-locations/entrypoint.png)

3. Dans la page **Accès conditionnel**, sélectionnez **Emplacements nommés**, puis **Nouvel emplacement**.

    ![Emplacements nommés](./media/quickstart-configure-named-locations/namedlocation.png)

6. Remplissez le formulaire dans la nouvelle page. 

    * Dans la zone de texte **Nom**, tapez un nom pour votre emplacement nommé.
    * Dans la zone **Plages d’adresses IP**, tapez la plage d’adresses IP au format CIDR.  
    * Cliquez sur **Créer**.
    
    ![Panneau Nouveau](./media/quickstart-configure-named-locations/61.png)

## <a name="next-steps"></a>Étapes suivantes

Pour plus d'informations, consultez les pages suivantes :

- [Accès conditionnel Azure AD](../active-directory-conditional-access-azure-portal.md)
- [Conditions d’emplacement dans l’accès conditionnel Azure AD](../conditional-access/location-condition.md)
- [Rapport sur les connexions à risque](concept-risky-sign-ins.md)  
