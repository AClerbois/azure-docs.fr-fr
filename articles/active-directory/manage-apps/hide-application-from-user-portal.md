---
title: Masquer une application de l’expérience utilisateur dans Azure Active Directory | Microsoft Docs
description: Guide pratique pour masquer une application depuis l’expérience utilisateur dans les panneaux d’accès Azure Active Directory ou les lanceurs Office 365.
services: active-directory
author: barbkess
manager: daveba
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: barbkess
ms.reviewer: kasimpso
ms.openlocfilehash: d5443f5b33f85d7a4b1e69cecc6bcdf68859abf4
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54474899"
---
# <a name="hide-applications-from-end-users-in-azure-active-directory"></a>Masquer les applications aux utilisateurs finaux dans Azure Active Directory

Instructions pour masquer les applications du panneau MyApps ou du Lanceur Office 365 à l’utilisateur final. Lorsqu’une application est masquée, les utilisateurs ont toujours les autorisations à celle-ci. 

## <a name="prerequisites"></a>Prérequis

Les privilèges d’administrateur d’application sont nécessaires pour masquer une application du panneau MyApps et du Lanceur Office 365.

Les privilèges d’administrateur général sont nécessaires pour masquer toutes les applications Office 365.


## <a name="hide-an-application-from-the-end-user"></a>Masquer une application à l’utilisateur final
Suivez ces étapes pour masquer une application dans le panneau MyApps et dans le lanceur d’applications Office 365.

1.  Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur général de votre annuaire.
2.  Sélectionnez **Azure Active Directory**.
3.  Sélectionnez **Applications d’entreprise**. Le panneau **Applications d’entreprise - Toutes les applications** s’ouvre.
4.  Sous **Type d’Application**, sélectionnez **Applications d’entreprise** si cette option n’est pas encore sélectionnée.
5.  Recherchez l’application que vous souhaitez masquer, puis cliquez dessus.  La vue d’ensemble de l’application apparaît.
6.  Cliquez sur **Propriétés**. 
7.  À la question **Visible par les utilisateurs ?**, cliquez sur **Non**.
8.  Cliquez sur **Enregistrer**.


## <a name="hide-office-365-applications-from-the-myapps-panel"></a>Masquer des applications Office 365 dans le panneau MyApps

Procédez comme suit pour masquer toutes les applications Office 365 dans le panneau MyApps. Les applications sont toujours visibles dans le portail Office 365.

1.  Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur général de votre annuaire.
2.  Sélectionnez **Azure Active Directory**.
3.  Sélectionnez **Paramètres utilisateur**.
4.  Sous **Applications d’entreprise**, cliquez sur **Gérer la façon dont les utilisateurs finaux lancent et affichent leurs applications**.
5.  Pour **Les utilisateurs peuvent voir uniquement les applications Office 365 dans le portail Office 365**, cliquez sur **Oui**.
6.  Cliquez sur **Enregistrer**.


## <a name="next-steps"></a>Étapes suivantes
* [Voir tous mes groupes](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Affecter un utilisateur ou un groupe à une application d’entreprise](assign-user-or-group-access-portal.md)
* [Suppression d’une affectation d’utilisateur ou de groupe à partir d’une application d’entreprise](remove-user-or-group-access-portal.md)
* [Modifier le nom ou le logo d’une application d’entreprise dans la version préliminaire d’Azure Active Directory](change-name-or-logo-portal.md)

