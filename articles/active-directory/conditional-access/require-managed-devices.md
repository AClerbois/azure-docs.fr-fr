---
title: 'Guide pratique : Exiger des appareils gérés pour accéder aux applications cloud avec l’accès conditionnel Azure Active Directory | Microsoft Docs'
description: Découvrez comment configurer des stratégies d’accès conditionnel Azure Active Directory (Azure AD) basées sur les appareils qui exigent des appareils gérés pour accéder aux applications cloud.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: a27862a6-d513-43ba-97c1-1c0d400bf243
ms.service: active-directory
ms.subservice: conditional-access
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2018
ms.author: markvi
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0c468f149a8005f2452ba49afbb447c3304c3d4b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58170528"
---
# <a name="how-to-require-managed-devices-for-cloud-app-access-with-conditional-access"></a>Procédure : Exiger des appareils gérés pour accéder aux applications cloud avec l’accès conditionnel

Dans un monde où la mobilité et le cloud occupent le premier plan, Azure Active Directory (Azure AD) vous permet de vous authentifier à l’aide de l’authentification unique sur les applications et les services, où que vous soyez. Les utilisateurs autorisés peuvent accéder à vos applications cloud à partir d’un large éventail d’appareils, y compris les appareils mobiles et personnels. Toutefois, de nombreux environnements comprennent des applications qui ne doivent être accessibles que par des appareils répondant à vos standards de conformité et de sécurité. Ces appareils sont également appelés « appareils gérés ». 

Cet article explique comment vous pouvez configurer des stratégies d’accès conditionnel qui exigent des appareils gérés pour accéder à certaines applications cloud de votre environnement. 


## <a name="prerequisites"></a>Conditions préalables

Le fait d’exiger des appareils gérés pour accéder aux applications cloud associe **l’accès conditionnel Azure AD** et la **gestion des appareils Azure AD**. Si l’un de ces aspects vous est inconnu, consultez d’abord les rubriques suivantes :

- **[Accès conditionnel dans Azure Active Directory](../active-directory-conditional-access-azure-portal.md)** : cet article fournit une vue d’ensemble conceptuelle de l’accès conditionnel et explique la terminologie associée.

- **[Présentation de la gestion des appareils dans Azure Active Directory](../devices/overview.md)** : cet article vous donne une vue d’ensemble des différentes options dont vous disposez pour faire passer les appareils sous le contrôle de l’organisation. 


## <a name="scenario-description"></a>Description du scénario

Il n’est pas facile de trouver le bon équilibre entre sécurité et productivité. L’augmentation du nombre d’appareils pris en charge pour accéder aux ressources cloud permet d’améliorer la productivité de vos utilisateurs. Toutefois, il est probable que vous ne souhaitiez pas que certaines ressources de votre environnement soient accessibles par des appareils dont le niveau de protection est inconnu. Pour ce type de ressources, vous devez exiger que les utilisateurs y accèdent uniquement à l’aide d’un appareil géré. 

L’accès conditionnel Azure AD permet de répondre à cette exigence avec une seule stratégie qui accorde l’accès :

- à certaines applications cloud ;

- pour certains utilisateurs et groupes ;

- Exiger un appareil géré


## <a name="managed-devices"></a>Appareils gérés  

En termes simples, les appareils gérés sont ceux que l’organisation peut contrôler *d’une manière ou d’une autre*. Dans Azure AD, les prérequis pour un appareil géré implique son inscription auprès d’Azure AD. L’inscription d’un appareil crée une identité pour lui sous la forme d’un objet appareil. Cet objet est utilisé par Azure pour effectuer le suivi des informations d’état relatives à un appareil. En tant qu’administrateur Azure AD, vous pouvez déjà utiliser cet objet pour permuter (activer/désactiver) l’état d’un appareil.
  
![Conditions basées sur l’appareil](./media/require-managed-devices/32.png)

Pour inscrire un appareil auprès d’Azure AD, vous avez trois possibilités :

- **[Appareils inscrits sur Azure AD](../devices/overview.md#azure-ad-registered-devices)**  : pour inscrire un appareil personnel auprès d’Azure AD

- **[Appareils joints à Azure AD](../devices/overview.md#azure-ad-joined-devices)**  : pour inscrire auprès d’Azure AD un appareil Windows 10 professionnel qui n’est pas joint à une version locale d’AD. 

- **[Appareils joints à une version hybride d’Azure AD](../devices/overview.md#hybrid-azure-ad-joined-devices)** : pour inscrire auprès d’Azure AD un appareil Windows 10 ou de bas niveau pris en charge joint à un AD local.

Pour avoir le statut d’appareil géré, un appareil inscrit doit être soit un **appareil hybride joint à Azure AD**, soit un **appareil marqué comme conforme**.  

![Conditions basées sur l’appareil](./media/require-managed-devices/47.png)

 
## <a name="require-hybrid-azure-ad-joined-devices"></a>Exiger un appareil joint à une version hybride d’Azure AD

Dans votre stratégie d’accès conditionnel, vous pouvez sélectionner **Exiger un appareil joint à une version hybride d’Azure AD** pour indiquer que les applications cloud sélectionnées sont uniquement accessibles à l’aide d’un appareil géré. 

![Conditions basées sur l’appareil](./media/require-managed-devices/10.png)

Ce paramètre s’applique uniquement à Windows 10 ou aux appareils de bas niveau tels que Windows 7 ou Windows 8 qui sont joints à un AD local. Vous pouvez uniquement inscrire ces appareils auprès d’Azure AD en utilisant une jointure à une version hybride d’Azure AD, ce qui correspond à un [processus automatisé](../devices/hybrid-azuread-join-plan.md) pour inscrire un appareil Windows 10. 

![Conditions basées sur l’appareil](./media/require-managed-devices/45.png)

Qu’est-ce qui transforme un appareil joint à une version hybride d’Azure AD en appareil géré ?  Pour les appareils joints à une version locale d’Active Directory, le contrôle est supposé être appliqué à l’aide de solutions de gestion comme **System Center Configuration Manager (SCCM)** ou une **stratégie de groupe**. Étant donné qu’il n’existe aucune méthode permettant à Azure AD de déterminer si une de ces méthodes a été appliquée à un appareil, exiger un appareil joint à une version hybride d’Azure AD constitue un mécanisme relativement faible pour exiger un appareil géré. Il vous appartient en tant qu’administrateur de déterminer si les méthodes appliquées à vos appareils joints au domaine local sont suffisamment fortes pour constituer un appareil géré si ces appareils sont également joints à une version hybride d’Azure AD.


## <a name="require-device-to-be-marked-as-compliant"></a>Exiger que l’appareil soit marqué comme conforme

L’option *Exiger que l’appareil soit marqué comme conforme* est la façon la plus forte de demander un appareil géré.

![Conditions basées sur l’appareil](./media/require-managed-devices/11.png)

Cette option exige qu’un appareil soit inscrit auprès d’Azure AD et qu’il soit marqué comme conforme par :
         
- Intune.
- Système MDM tiers qui gère les appareils Windows 10 via l’intégration d’Azure AD. Les systèmes MDM tiers pour les systèmes d’exploitation autres que Windows 10 ne sont pas pris en charge.
 
![Conditions basées sur l’appareil](./media/require-managed-devices/46.png)



Pour un appareil marqué comme conforme, vous pouvez partir du principe que : 

- Les appareils mobiles que votre personnel utilise pour accéder aux données d’entreprise sont gérés.
- Les appareils mobiles que votre personnel utilise sont gérés.
- Les informations de votre entreprise sont protégées grâce au contrôle de la manière dont votre personnel y accède et les partage.
- L’appareil et ses applications sont conformes aux exigences de sécurité de l’entreprise.




## <a name="next-steps"></a>Étapes suivantes

Avant de configurer une stratégie d’accès conditionnel basé sur les appareils dans votre environnement, consultez les [meilleures pratiques pour l’accès conditionnel dans Azure Active Directory](best-practices.md).

