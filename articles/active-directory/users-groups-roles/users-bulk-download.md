---
title: Télécharger une liste d’utilisateurs (préversion) dans le portail Azure Active Directory | Microsoft Docs
description: Téléchargez des enregistrements d’utilisateur en bloc dans le centre d’administration Azure dans Azure Active Directory.
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 07/15/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: c59a0b40065be1554e79186227cd2e90d68e7222
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72174206"
---
# <a name="download-a-list-of-users-preview-in-azure-active-directory-portal"></a>Télécharger une liste d’utilisateurs (préversion) dans le portail Azure Active Directory

Azure Active Directory (Azure AD) prend en charge les opérations d’importation (création) d’utilisateurs en bloc.

## <a name="required-permissions"></a>Autorisations requises

Pour télécharger la liste des utilisateurs à partir du centre d’administration Azure AD, vous devez être connecté avec un ID d’utilisateur associé à un ou plusieurs rôles Administrateur au niveau de l’organisation dans Azure AD. Les inviteurs d’invités et les développeurs d’applications ne sont pas considérés comme des rôles Administrateur.

## <a name="to-download-a-list-of-users"></a>Pour télécharger une liste d’utilisateurs

1. [Connectez-vous à votre organisation](https://aad.portal.azure.com) Azure AD avec un compte Administrateur d’utilisateurs de l’organisation.
1. Dans Azure AD, sélectionnez **Utilisateurs** > **Télécharger les utilisateurs**.
1. Dans la page **Télécharger les utilisateurs**, sélectionnez **Démarrer** pour recevoir un fichier CSV répertoriant les propriétés de profil utilisateur. Si des erreurs se produisent, vous pouvez télécharger et consulter le fichier de résultats sur la page Résultats de l’opération en bloc. Le fichier contient la raison de chaque erreur.

   ![Sélectionnez l’emplacement où vous souhaitez télécharger la liste des utilisateurs](./media/users-bulk-download/bulk-download.png)

## <a name="check-status"></a>Vérification du statut

Vous pouvez voir l’état de vos demandes d’opération en bloc en attente dans la page **Résultats de l’opération en bloc (préversion)** .

   ![Vérifier l’état du chargement dans la page Résultats de l’opération en bloc](./media/users-bulk-download/bulk-center.png)

## <a name="bulk-download-service-limits"></a>Limites du service de téléchargement en bloc

Chaque opération en bloc de création de liste d’utilisateurs peut prendre jusqu’à une heure. Cela permet la création et le téléchargement d’une liste d’au moins 500 000 utilisateurs.

## <a name="next-steps"></a>Étapes suivantes

- [Ajouter des utilisateurs en bloc](users-bulk-add.md)
- [Supprimer des utilisateurs en bloc](users-bulk-delete.md)
- [Restaurer des utilisateurs en bloc](users-bulk-restore.md)
