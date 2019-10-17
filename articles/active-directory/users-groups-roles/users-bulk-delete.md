---
title: Supprimer des utilisateurs en bloc (préversion) dans le portail Azure Active Directory | Microsoft Docs
description: Supprimer des utilisateurs en bloc dans le centre d’administration Azure dans Azure Active Directory
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 08/15/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: d7c47887c12c8bf9be7a0c5b11dfb3f099965cb7
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72174373"
---
# <a name="bulk-delete-users-preview-in-azure-active-directory"></a>Supprimer des utilisateurs en bloc (préversion) dans Azure Active Directory

En utilisant le portail Azure Active Directory (Azure AD), vous pouvez supprimer un grand nombre de membres d’un groupe en utilisant un fichier de valeurs séparées par des virgules (CSV) pour supprimer en bloc les utilisateurs.

## <a name="to-bulk-delete-users"></a>Pour supprimer des utilisateurs en bloc

1. [Connectez-vous à votre organisation Azure AD](https://aad.portal.azure.com) avec un compte Administrateur d’utilisateurs de celle-ci.
1. Dans Azure AD, sélectionnez **Utilisateurs** > **Supprimer en bloc**.
1. Dans la page **Supprimer des utilisateurs en bloc**, sélectionnez **Télécharger** pour recevoir un fichier CSV de propriétés utilisateur valide.

   ![Sélectionner un fichier CSV local dans lequel vous répertoriez les utilisateurs à supprimer](./media/users-bulk-delete/bulk-delete.png)

1. Ouvrez le fichier CSV et ajoutez une ligne pour chaque utilisateur à supprimer. La seule valeur obligatoire est **Nom d’utilisateur principal**. Puis enregistrez le fichier.

   ![Le fichier CSV contient les noms et les ID des utilisateurs à supprimer.](./media/users-bulk-delete/delete-csv-file.png)

1. Dans la page **Supprimer des utilisateurs en bloc (préversion)** , sous **Charger votre fichier csv**, accédez au fichier. Quand vous sélectionnez le fichier et cliquez sur Envoyer, la validation du fichier CSV démarre.
1. Quand le contenu du fichier est validé, un message indique **Fichier chargé**. Si des erreurs sont présentes, vous devez les corriger avant de pouvoir envoyer le travail.
1. Lorsque votre fichier réussit la validation, sélectionnez **Envoyer** pour démarrer l’opération en bloc Azure qui supprime les utilisateurs.
1. Une fois l’opération de suppression terminée, vous recevez une notification indiquant que l’opération en bloc a réussi.

Si des erreurs se produisent, vous pouvez télécharger et consulter le fichier de résultats dans la page **Résultats de l’opération en bloc**. Le fichier contient la raison de chaque erreur.

## <a name="check-status"></a>Vérification du statut

Vous pouvez voir l’état de toutes vos demandes d’opération en bloc en attente dans la page **Résultats de l’opération en bloc (préversion)** .

   ![Vérifier l’état du chargement dans la page Résultats de l’opération en bloc](./media/users-bulk-delete/bulk-center.png)

Ensuite, vous pouvez vérifier si les utilisateurs que vous avez supprimés n’existent plus au sein de l’organisation Azure AD via le portail Azure ou à l’aide de PowerShell.

## <a name="verify-deleted-users-in-the-azure-portal"></a>Vérifier la suppression d’utilisateurs via le portail Azure

1. Connectez-vous au portail Azure en utilisant un compte d’administrateur d’utilisateurs de l’organisation.
1. Dans le volet de navigation, sélectionnez **Azure Active Directory**.
1. Sous **Gérer**, sélectionnez **Utilisateurs**.
1. Sous **Afficher**, sélectionnez **Tous les utilisateurs** et vérifiez que les utilisateurs que vous avez supprimés ne sont plus répertoriés.

### <a name="verify-deleted-users-with-powershell"></a>Vérifier la suppression d’utilisateurs à l’aide de PowerShell

Exécutez la commande suivante :

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Vérifiez que les utilisateurs que vous avez supprimés ne sont plus répertoriés.

## <a name="next-steps"></a>Étapes suivantes

- [Ajouter des utilisateurs en bloc](users-bulk-add.md)
- [Télécharger une liste d’utilisateurs](users-bulk-download.md)
- [Restaurer des utilisateurs en bloc](users-bulk-restore.md)
