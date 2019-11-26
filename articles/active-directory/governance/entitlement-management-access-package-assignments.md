---
title: Afficher, ajouter et supprimer des affectations pour un package d’accès dans la gestion des droits d’utilisation d’Azure Active Directory - Azure Active Directory
description: Découvrez comment afficher, ajouter et supprimer des affectations pour un package d’accès dans la gestion des droits d’utilisation d’Azure Active Directory.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3f06da43e758c24da65664b34dd83f876a00e3a0
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73174899"
---
# <a name="view-add-and-remove-assignments-for-an-access-package-in-azure-ad-entitlement-management"></a>Afficher, ajouter et supprimer des affectations pour un package d’accès dans la gestion des droits d’utilisation d’Azure Active Directory

Dans la gestion des droits d'utilisation Azure AD, vous pouvez voir qui a été affecté aux packages d’accès, ainsi que leur stratégie et leur état. Si un package d’accès a une stratégie appropriée, vous pouvez également affecter directement l’utilisateur à un package d’accès. Cet article explique comment afficher, ajouter et supprimer des affectations pour des packages d’accès.

## <a name="view-who-has-an-assignment"></a>Afficher les utilisateurs qui ont une affectation

**Rôle prérequis :** Administrateur général, administrateur d’utilisateurs ou gestionnaire de package d’accès

1. Dans le portail Azure, cliquez sur **Azure Active Directory**, puis sur **Identity Governance**.

1. Dans le menu de gauche, cliquez sur **Packages d’accès**, puis ouvrez le package d'accès.

1. Cliquez sur **Affectations** pour afficher une liste des affectations actives.

    ![Liste des affectations à un package d’accès](./media/entitlement-management-access-package-assignments/assignments-list.png)

1. Cliquez sur une affectation spécifique pour voir plus de détails.

1. Pour afficher une liste des affectations dont les rôles de ressources n’étaient pas correctement provisionnés, cliquez sur filtre d’état et sélectionnez **Livraison en cours**.

    Vous pouvez voir des détails supplémentaires sur les erreurs de remise en recherchant la requête correspondante de l'utilisateur sur la page **Requêtes**.

1. Pour voir les affectations expirées, cliquez sur le filtre d’état et sélectionnez **Expiré**.

1. Pour télécharger un fichier CSV contenant la liste filtrée, cliquez sur **Télécharger**.

## <a name="directly-assign-a-user"></a>Affecter directement un utilisateur

Dans certains cas, vous pouvez affecter directement des utilisateurs spécifiques à un package d'accès pour leur éviter d’avoir à passer par le processus de demande du package d'accès. Pour affecter directement des utilisateurs, le package d'accès doit avoir une stratégie qui autorise les affectations directes par l'administrateur.

**Rôle prérequis :** Administrateur général, administrateur d’utilisateurs ou gestionnaire de package d’accès

1. Dans le portail Azure, cliquez sur **Azure Active Directory**, puis sur **Identity Governance**.

1. Dans le menu de gauche, cliquez sur **Packages d’accès**, puis ouvrez le package d'accès.

1. Dans le menu gauche, cliquez sur **Affectations**.

1. Cliquez sur **Nouvelle affectation** pour ouvrir la fenêtre Ajouter un utilisateur au package d’accès.

    ![Affectations - Ajouter un utilisateur au package d’accès](./media/entitlement-management-access-package-assignments/assignments-add-user.png)

1. Cliquez sur **Ajouter des utilisateurs** pour sélectionner les utilisateurs auxquels vous voulez affecter ce package d’accès.

1. Dans la liste **Sélectionner une stratégie**, sélectionnez une stratégie selon laquelle les requêtes et le cycle de vie des utilisateurs seront régis et suivis. Si vous souhaitez que les utilisateurs sélectionnés aient des paramètres de stratégie différents, vous pouvez cliquer sur **Créer une nouvelle stratégie** pour ajouter une nouvelle stratégie.

1. Définissez la date et l'heure de début et de fin de l'affectation des utilisateurs sélectionnés. Si aucune date de fin n’est fournie, les paramètres du cycle de vie de la stratégie seront utilisés.

1. Si vous le souhaitez, fournissez une justification de votre affectation directe à des fins d’archivage.

1. Cliquez sur **Ajouter** pour affecter directement les utilisateurs sélectionnés au package d'accès.

    Après quelques instants, cliquez sur **Actualiser** pour voir les utilisateurs dans la liste des affectations.

## <a name="next-steps"></a>Étapes suivantes

- [Changer la demande et les paramètres pour un package d’accès](entitlement-management-access-package-request-policy.md)
- [Afficher les rapports et les journaux](entitlement-management-reports.md)
