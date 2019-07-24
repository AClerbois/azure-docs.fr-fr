---
title: Gérer l’accès aux applications à l’aide d’Azure AD | Microsoft Docs
description: Décrit comment Azure Active Directory permet aux organisations de spécifier les applications auxquelles chaque utilisateur a accès.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2017
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2f86dfd414309e8420dc19c92c1cd20c40a5a13a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67108185"
---
# <a name="managing-access-to-apps"></a>Gestion de l’accès aux applications
La gestion de l’accès, l’évaluation de l’utilisation et la création de rapports en continu demeurent un défi quand une application vient d’être intégrée au système d’identité de votre organisation. Dans de nombreux cas, les administrateurs informatiques ou le support technique doivent en permanence jouer un rôle actif dans la gestion de l’accès à vos applications. Parfois, l’affectation est effectuée par une équipe informatique générale ou rattachée à une division. Souvent, il revient au décideur d’entreprise de décider d’une affectation, que l’équipe informatique ne pourra mettre en œuvre qu’avec son approbation.  D’autres organisations investissent dans l’intégration à un système automatisé de gestion des identités et des accès, tel que le contrôle d’accès en fonction du rôle (RBAC) ou le contrôle d’accès en fonction de l’attribut (ABAC). L’intégration et le développement de règles ont tous deux tendance à être spécialisés et coûteux. Quelle que soit la méthode de gestion, l’analyse ou la création de rapports suppose un investissement distinct, coûteux et complexe.

## <a name="how-does-azure-active-directory-help"></a>En quoi Azure Active Directory peut-il vous aider ?
 Azure AD prend en charge une gestion complète de l’accès pour les applications configurées, permettant aux organisations d’accomplir facilement les stratégies d’accès appropriées, allant de l’affectation automatique basée sur l’attribut (scénarios ABAC ou RBAC) à la gestion des administrateurs en passant par la délégation. Grâce à Azure AD, vous pouvez facilement accomplir des stratégies complexes, en combinant plusieurs modèles de gestion pour une application unique, et pouvez même réutiliser les règles de gestion entre les applications avec le même public.

* [Ajout d’applications nouvelles ou existantes](configure-single-sign-on-portal.md)

  L’affectation d’applications Azure AD se concentre sur deux modes d’affectation principaux :

* **Affectation individuelle** : un administrateur informatique détenant des autorisations d’administrateur général de l’annuaire peut sélectionner différents comptes d’utilisateurs et leur octroyer un accès à l’application.
* **Affectation basée sur le groupe (version payante d’Azure AD uniquement)** : un administrateur informatique détenant des autorisations d’administrateur général de l’annuaire peut affecter un groupe à l’application. L’accès dont bénéficie un utilisateur dépend de son appartenance éventuelle au groupe au moment où il essaie d’accéder à l’application. En d’autres termes, un administrateur peut créer efficacement une règle d’affectation indiquant « tout membre actuel du groupe affecté a accès à l’application ». Avec cette option d’affectation, les administrateurs peuvent tirer parti des options de gestion de groupe Azure AD, notamment des [groupes dynamiques basés sur l’attribut](../fundamentals/active-directory-groups-create-azure-portal.md), des groupes de systèmes externes (par exemple, Active Directory local ou Workday) ou des groupes gérés par un administrateur ou en libre-service. Un même groupe peut être facilement affecté à plusieurs applications ; de la sorte, celles qui présentent une affinité d’affectation peuvent partager des règles d’affectation, réduisant ainsi la complexité globale de la gestion. Notez que des appartenances à des groupes imbriquées ne sont pas prises en charge pour l’affectation basée sur le groupe à des applications à ce stade.

Grâce à ces deux modes d’affectation, les administrateurs peuvent mettre en œuvre toute approche de gestion d’affectation souhaitable.

Avec Azure AD, la création de rapports d’utilisation et d’affectation est entièrement intégrée, permettant aux administrateurs de créer facilement des rapports sur l’état des affectations, les erreurs d’affectation et même sur l’utilisation.

## <a name="complex-application-assignment-with-azure-ad"></a>Affectation d’application complexe avec Azure AD
Considérez une application comme Salesforce. Dans de nombreuses organisations, Salesforce est principalement utilisé par des équipes commerciales et marketing. Souvent, les membres de l’équipe marketing disposent d’un accès hautement privilégié à Salesforce, contrairement aux membres de l’équipe de ventes. Dans de nombreux cas, la majorité des travailleurs de l’information n’a qu’un accès limité à l’application. Les exceptions à ces règles compliquent les choses. C’est souvent la prérogative des équipes de direction marketing ou de ventes d’accorder un accès à un utilisateur ou de modifier son rôle indépendamment de ces règles génériques.

Avec Azure AD, les applications telles que Salesforce peuvent être préconfigurées pour l’authentification unique (SSO) et l’automatisation de l’approvisionnement. Dès que l’application est configurée, un administrateur peut créer et affecter des groupes appropriés une bonne fois pour toutes. Dans cet exemple, un administrateur peut exécuter les affectations suivantes :

* [groupes dynamiques](../fundamentals/active-directory-groups-create-azure-portal.md) peuvent être définis pour représenter automatiquement tous les membres des équipes de ventes et de marketing à l’aide d’attributs tels que le service ou le rôle :
  
  * Tous les membres de groupes marketing sont affectés au rôle « marketing » dans Salesforce.
  * Tous les membres de groupes de ventes sont affectés au rôle « ventes » dans Salesforce. Pour affiner les choses, plusieurs groupes représentant des équipes de ventes régionales affectées à différents rôles Salesforce peuvent être utilisés.
* Pour activer le mécanisme d’exception, un groupe libre-service peut être créé pour chaque rôle. Par exemple, le groupe « exception marketing dans Salesforce » peut être créé en tant que groupe libre-service. Le groupe peut être affecté au rôle marketing dans Salesforce, tandis que les membres de l’équipe de direction marketing peuvent être définis en tant que propriétaires. Les membres de l’équipe de direction marketing peuvent ajouter ou supprimer des utilisateurs, définir une stratégie de jonction ou même approuver ou refuser les demandes de jonction formulées par des utilisateurs spécifiques. Ce mécanisme repose sur une expérience de travailleur de l’information appropriée qui ne nécessite pas de formation spécialisée pour les propriétaires ou les membres.

Dans ce cas, tous les utilisateurs affectés sont automatiquement approvisionnés dans Salesforce ; quand ils sont ajoutés aux différents groupes, leur affectation de rôle est actualisée dans Salesforce. Les utilisateurs peuvent découvrir Salesforce et y accéder par le biais du panneau d’accès des applications Microsoft, des clients web Office ou même de la page de connexion à Salesforce propre à leur organisation. Les administrateurs peuvent facilement afficher l’état de l’utilisation et des affectations à l’aide de rapports d’Azure AD.

Les administrateurs peuvent utiliser l’ [accès conditionnel Azure AD](../active-directory-conditional-access-azure-portal.md) pour définir des stratégies d’accès pour des rôles spécifiques. Ces stratégies peuvent indiquer si l’accès est autorisé en dehors de l’environnement de l’entreprise, et même inclure des exigences Multi-Factor Authentication ou liées aux appareils déterminant l’octroi de l’accès dans divers cas.

## <a name="next-steps"></a>Étapes suivantes
* [Protection des applications avec accès conditionnel](../active-directory-conditional-access-azure-portal.md)
* [Gestion des groupes en libre service/accès aux applications en libre-service](../users-groups-roles/groups-self-service-management.md)
