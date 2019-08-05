---
title: Que sont les révisions d’accès ? - Azure Active Directory | Microsoft Docs
description: Avec les révisions d’accès Azure Active Directory, vous pouvez contrôler que l’appartenance à des groupes et l’accès aux applications répondent aux initiatives de gouvernance, de gestion des risques et de conformité de votre organisation.
services: active-directory
documentationcenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 07/23/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: da9bc3906e6f39b2d943708eb6a1b930ac8cc5a5
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68401945"
---
# <a name="what-are-azure-ad-access-reviews"></a>Présentation des révisions d’accès Azure AD

Les révisions d’accès Azure Active Directory (Azure AD) permettent aux organisations de gérer efficacement les appartenances à des groupes, les accès aux applications d’entreprise et les attributions de rôles. L’accès des utilisateurs peut être passé en revue régulièrement pour vérifier que seules les personnes appropriées continuent de bénéficier d’un accès.

Voici une vidéo qui donne une vue d’ensemble rapide des révisions d’accès :

>[!VIDEO https://www.youtube.com/embed/kDRjQQ22Wkk]

## <a name="why-are-access-reviews-important"></a>Pourquoi les révisions d’accès sont-elles importantes ?

Azure AD vous permet de collaborer en interne au sein de votre organisation et avec les utilisateurs d’organisations externes, comme des partenaires. Les utilisateurs peuvent se joindre à des groupes, inviter des personnes, se connecter à des applications cloud et travailler à distance à partir de leurs appareils personnels ou professionnels. L’intérêt de bénéficier de la puissance du libre-service a conduit à la nécessité de meilleures fonctionnalités de gestion des accès.

- Quand de nouveaux employés arrivent, comment vérifier qu’ils disposent des bons accès leur permettant d’être productifs ?
- Quand des personnes changent d’équipe ou quittent l’entreprise, comment vérifier que leur ancien accès est bien supprimé, en particulier quand il implique des invités ?
- Des droits d’accès excessifs peuvent amener à auditer les résultats et les compromissions, car ils signalent un manque de contrôle des accès.
- Vous devez inviter de façon proactive les propriétaires des ressources à vérifier régulièrement les utilisateurs qui ont accès à leurs ressources.

## <a name="when-to-use-access-reviews"></a>Quand utiliser les révisions d’accès ?

- **Trop d’utilisateurs dans des rôles privilégiés :** Il est judicieux de vérifier combien d’utilisateurs ont un accès d’administration, combien d’entre eux ont le rôle d’administrateur général, et s’il existe des invités ou des partenaires qui n’ont pas été supprimés après qu’une tâche d’administration leur a été attribuée. Vous pouvez recertifier les utilisateurs ayant fait l’objet d’une attribution de rôle dans les [rôles Azure AD](../privileged-identity-management/pim-how-to-perform-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json), comme les administrateurs généraux, ou [les rôles de ressources Azure](../privileged-identity-management/pim-resource-roles-perform-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json), comme les administrateur de l’accès utilisateur dans l’expérience [Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md).
- **Quand l’automatisation est irréalisable :** Vous pouvez créer des règles pour l’appartenance dynamique sur les groupes de sécurité ou les groupes Office 365, mais que se passe-t-il si les données des ressources humaines ne se trouvent pas dans Azure AD, ou si des utilisateurs ont toujours besoin d’un accès après avoir quitté le groupe pour former les personnes qui les remplacent ? Vous pouvez alors créer une révision sur ce groupe pour que ceux qui ont encore besoin d’un accès puissent encore en bénéficier.
- **Quand un groupe est utilisé pour un nouvel objectif :** Si vous avez un groupe qui va être synchronisé avec Azure AD, ou si vous prévoyez d’activer l’application Salesforce pour tout le monde dans le groupe de l’équipe commerciale, il est utile de demander au propriétaire du groupe de passer en revue l’appartenance au groupe avant d’utiliser le groupe dans un autre contenu à risques.
- **Accès aux données critiques de l’entreprise :** pour certaines ressources, il peut être nécessaire à des fins d’audit de demander aux personnes en dehors du département informatique de se déconnecter régulièrement et de justifier la raison pour laquelle ils ont besoin d’un accès.
- **Pour gérer la liste d’exceptions d’une stratégie :** Dans l’idéal, tous les utilisateurs doivent respecter les stratégies d’accès pour sécuriser l’accès aux ressources de votre organisation. Toutefois, certains scénarios métiers nécessitent que vous fassiez des exceptions. En tant qu’administrateur informatique, vous pouvez gérer cette tâche, éviter d’oublier les exceptions à la stratégie et fournir aux auditeurs la preuve que ces exceptions sont révisées régulièrement.
- **Demander aux propriétaires de groupe de confirmer qu’ils ont encore besoin d’invités dans leurs groupes :** L’accès des employés peut être automatisé avec certains systèmes IAM locaux, mais pas l’accès des invités. Si un groupe donne à des invités l’accès à du contenu sensible de l’entreprise, il est de la responsabilité du propriétaire du groupe de confirmer que les invités ont encore un besoin métier légitime de cet accès.
- **Procédez régulièrement à des révisions d’accès :** Vous pouvez configurer des révisions d’accès des utilisateurs récurrentes à des fréquences définies, hebdomadaires, mensuelles, trimestrielles ou annuelles, les réviseurs étant alors informés au début de chaque révision. Les réviseurs peuvent approuver ou refuser l’accès avec une interface conviviale et avec l’aide de recommandations intelligentes.

## <a name="where-do-you-create-reviews"></a>Où créer des révisions ?

Selon ce que vous voulez réviser, vous créez votre révision d’accès dans Révisions d’accès Azure AD, dans Applications d’entreprise Azure AD (en préversion) ou dans Azure AD PIM.

| Droits d’accès des utilisateurs | Les réviseurs peuvent être | Révision créée dans | Expérience des réviseurs |
| --- | --- | --- | --- |
| Membres des groupes de sécurité</br>Membres du groupe Office | Réviseurs spécifiés</br>Propriétaires de groupe</br>Révision indépendante | Révisions d’accès Azure AD</br>Groupes Azure AD | Panneau d’accès |
| Affecté à une application connectée | Réviseurs spécifiés</br>Révision indépendante | Révisions d’accès Azure AD</br>Applications d’entreprise Azure AD (en préversion) | Panneau d’accès |
| Rôle Azure AD | Réviseurs spécifiés</br>Révision indépendante | [Azure AD PIM](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | Portail Azure |
| Rôle de ressource Azure | Réviseurs spécifiés</br>Révision indépendante | [Azure AD PIM](../privileged-identity-management/pim-resource-roles-start-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | Portail Azure |

## <a name="which-users-must-have-licenses"></a>Quels utilisateurs doivent avoir des licences ?

Chaque utilisateur qui interagit avec les révisions d’accès doit posséder une licence Azure AD Premium P2 payante. Voici quelques exemples :

- Administrateurs qui créent une révision d’accès
- Propriétaires de groupe qui effectuent une révision d’accès
- Utilisateurs attribués en tant que réviseurs
- Utilisateurs qui effectuent une révision indépendante

Vous pouvez également demander aux utilisateurs invités de vérifier leur propre accès. Pour chaque licence Azure AD Premium P2 que vous attribuez à un utilisateur de votre organisation, vous pouvez utiliser Azure AD B2B pour inviter jusqu’à cinq utilisateurs dans le cadre d’une allocation d’utilisateur externe. Ces utilisateurs invités peuvent également utiliser les fonctionnalités d’Azure AD Premium P2. Pour plus d’informations, consultez les conseils sur l’[affectation de licences Azure AD B2B Collaboration](../b2b/licensing-guidance.md).

Voici quelques exemples de scénarios pour vous aider à déterminer le nombre de licences dont vous devez disposer.

| Scénario | Calcul | Nombre de licences requis |
| --- | --- | --- |
| Un administrateur crée une révision d’accès du groupe A avec 500 utilisateurs.<br/>Attribue 3 propriétaires de groupe en tant que réviseurs. | 1 administrateur + 3 propriétaires de groupe | 4 |
| Un administrateur crée une révision d’accès du groupe A avec 500 utilisateurs.<br/>Effectue une révision indépendante. | 1 administrateur + 500 utilisateurs en tant que réviseurs indépendants | 501 |
| Un administrateur crée une révision d’accès de groupe A avec 5 utilisateurs et 25 utilisateurs invités.<br/>Effectue une révision indépendante. | 1 administrateur + 5 utilisateurs en tant que réviseurs indépendants<br/>(les utilisateurs invités sont traités dans le rapport 1 à 5 requis) | 6 |
| Un administrateur crée une révision d’accès de groupe A avec 5 utilisateurs et 28 utilisateurs invités.<br/>Effectue une révision indépendante. | 1 administrateur + 5 utilisateurs en tant que réviseurs indépendants + 1 utilisateur pour couvrir les utilisateurs invités dans le rapport de 1 à 5 requis | 7 |

Pour plus d’informations sur l’attribution de licences aux utilisateurs, consultez [Assigner ou supprimer des licences à l’aide du portail Azure Active Directory](../fundamentals/license-users-groups.md).

## <a name="onboard-access-reviews"></a>Intégrer les révisions d’accès

Pour intégrer les révisions d’accès, effectuez les étapes suivantes.

1. Connectez-vous en tant qu’administrateur général ou administrateur d’utilisateurs au [portail Azure](https://portal.azure.com) où vous voulez utiliser les révisions d’accès.

1. Dans le volet de navigation à gauche, cliquez sur **Azure Active Directory**.

1. Dans le menu gauche, cliquez sur **Identity Governance**.

1. Cliquez sur **Révisions d’accès**.
 
    ![Page de démarrage des révisions d'accès](./media/access-reviews-overview/access-reviews-overview-onboard.png)

1. Sur la page, cliquez sur le bouton **Intégrer maintenant**.
    
      ![Intégration des révisions d’accès](./media/access-reviews-overview/access-reviews-overview-select-onboard.png)


## <a name="learn-about-access-reviews"></a>En savoir plus sur les révisions d’accès

Pour en savoir plus sur la création et la réalisation des révisions d’accès, regardez cette courte démonstration :

>[!VIDEO https://www.youtube.com/embed/6KB3TZ8Wi40]

Si vous êtes prêt à déployer des révisions d’accès dans votre organisation, effectuez les étapes décrites dans la vidéo pour effectuer l’intégration, former vos administrateurs et créer votre première révision d’accès !

>[!VIDEO https://www.youtube.com/embed/X1SL2uubx9M]

## <a name="license-requirements"></a>Conditions de licence :

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

## <a name="next-steps"></a>Étapes suivantes

- [Créer une révision d’accès de groupes ou d’applications](create-access-review.md)
- [Create an access review of users in an Azure AD administrative role](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) (Créer une révision d’accès pour les utilisateurs dans un rôle administratif d’Azure AD)
- [Réviser l’accès à des groupes ou à des applications](perform-access-review.md)
- [Effectuer une révision d’accès de groupes ou révisions d’accès des applications dans Azure AD](complete-access-review.md)
