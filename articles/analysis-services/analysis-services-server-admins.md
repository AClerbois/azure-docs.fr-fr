---
title: Gérer les administrateurs de serveur dans Azure Analysis Services | Microsoft Docs
description: Cet article explique comment gérer les administrateurs d’un serveur Azure Analysis Services avec le Portail Azure, PowerShell ou les API REST.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f7c57a5751f2ff34abb26b7653070ce4ee5010fe
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73572626"
---
# <a name="manage-server-administrators"></a>Gérer des administrateurs de serveur

Un administrateur de serveur doit être un utilisateur ou un groupe valide présent dans Azure Active Directory (Azure AD) pour le tenant disposant du serveur. Pour gérer les administrateurs de serveur, vous pouvez utiliser **Administrateurs Analysis Services** pour votre serveur dans le portail Microsoft Azure, ou Propriétés du serveur dans SSMS, PowerShell ou l’API REST. 

Les **groupes de sécurité** doivent être [à extension de messagerie](https://docs.microsoft.com/exchange/recipients-in-exchange-online/manage-mail-enabled-security-groups) avec la propriété `MailEnabled` définie sur `True`. Lorsque vous spécifiez un groupe par adresse e-mail, utilisez `obj:groupid@tenantid`.

## <a name="to-add-server-administrators-by-using-azure-portal"></a>Pour ajouter des administrateurs de serveur à l’aide du portail Azure

1. Dans le portail, cliquez sur **Administrateurs Analysis Services** pour votre serveur.
2. Dans **\<NomServeur> - Administrateurs Analysis Services**, cliquez sur **Ajouter**.
3. Dans **Ajouter des administrateurs de serveur**, sélectionnez des comptes d’utilisateur à partir d’Azure AD ou invitez des utilisateurs externes par le biais de leur adresse e-mail.

    ![Administrateurs de serveur dans le portail Azure](./media/analysis-services-server-admins/aas-manage-users-admins.png)

## <a name="to-add-server-administrators-by-using-ssms"></a>Pour ajouter des administrateurs de serveur à l’aide de SSMS

1. Faites un clic droit sur le serveur > **Propriétés**.
2. Dans **Propriétés de Analysis Server**, cliquez sur **Sécurité**.
3. Cliquez sur **Ajouter**, puis entrez l’adresse e-mail d’un utilisateur ou d’un groupe dans Azure AD.
   
    ![Ajouter des administrateurs de serveur dans SSMS](./media/analysis-services-server-admins/aas-manage-users-ssms.png)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Utilisez l’applet de commande [New-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/new-azanalysisservicesserver) pour spécifier le paramètre Administrateur au moment de la création d’un serveur. <br>
Utilisez l’applet de commande [Set-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver) pour modifier le paramètre Administrateur d’un serveur existant.

## <a name="rest-api"></a>API REST

Utilisez [Créer](https://docs.microsoft.com/rest/api/analysisservices/servers/create) pour spécifier la propriété asAdministrator au moment de créer un nouveau serveur. <br>
Utilisez [Mettre à jour](https://docs.microsoft.com/rest/api/analysisservices/servers/update) pour spécifier la propriété asAdministrator au moment de modifier un serveur existant. <br>



## <a name="next-steps"></a>Étapes suivantes 

[Authentification et autorisations utilisateur](analysis-services-manage-users.md)  
[Gérer les utilisateurs et rôles de base de données](analysis-services-database-users.md)  
[Contrôle d’accès en fonction du rôle](../role-based-access-control/overview.md)  

