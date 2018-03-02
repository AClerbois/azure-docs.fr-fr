---
title: "Azure Active Directory Domain Services : Dépannage de la configuration du principal de service | Microsoft Docs"
description: "Résolution des problèmes de configuration du principal de service pour les services de domaine Azure AD"
services: active-directory-ds
documentationcenter: 
author: eringreenlee
manager: 
editor: 
ms.assetid: f168870c-b43a-4dd6-a13f-5cfadc5edf2c
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/19/2018
ms.author: ergreenl
ms.openlocfilehash: 7388bb291f665f195355a01d19a82cba9ed453eb
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="troubleshoot-invalid-service-principal-configuration-for-your-managed-domain"></a>Résoudre les problèmes liés à une configuration de principal de service non valide pour votre domaine managé

Cet article vous aide à résoudre les erreurs liées à la configuration du principal de service qui entraînent l’affichage du message d’alerte suivant :

## <a name="alert-aadds102-service-principal-not-found"></a>Alerte AADDS102 : Principal de service introuvable
**Message d’alerte :**  *Un principal du service requis pour qu’Azure AD Domain Services fonctionne correctement a été supprimé de votre locataire Azure AD. Cette configuration affecte la capacité de Microsoft à surveiller, gérer, mettre à jour et synchroniser votre domaine managé.*

Les [principaux de service](../active-directory/develop/active-directory-application-objects.md) sont des applications que Microsoft utilise pour gérer, mettre à jour et maintenir votre domaine managé. S’ils sont supprimés, ils interrompent la capacité de Microsoft à travailler sur votre domaine. 


## <a name="check-for-missing-service-principals"></a>Vérifier les principaux de service manquants
Suivez les étapes ci-dessous pour déterminer les principaux de services qui doivent être recréés :

1. Accédez à la page [Applications d’entreprise - Toutes les applications](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps) dans le portail Azure.
2. Dans la liste déroulante **Afficher**, sélectionnez **Toutes les applications**, puis cliquez sur **Appliquer**.
3. Dans le tableau suivant, recherchez chaque ID d’application en collant l’ID dans la zone de recherche et en appuyant sur Entrée. Si les résultats de recherche sont vides, vous devez recréer le principal du service en suivant les étapes décrites dans la colonne « Résolution ».

| ID de l'application | Résolution : |
| :--- | :--- | :--- |
| 2565bd9d-da50-47d4-8b85-4c97f669dc36 | [Recréer un principal de service manquant avec PowerShell](#recreate-a-missing-service-principal-with-powershell) |
| 443155a6-77f3-45e3-882b-22b3a8d431fb | [Réinscrire l’espace de noms Microsoft.AAD](#re-register-to-the-microsoft-aad-namespace-using-the-azure-portal) |
| abba844e-bc0e-44b0-947a-dc74e5d09022  | [Réinscrire l’espace de noms Microsoft.AAD](#re-register-to-the-microsoft-aad-namespace-using-the-azure-portal) |
| d87dcbc6-a371-462e-88e3-28ad15ec4e64 | [Principaux de service avec correction autonome](#service-principals-that-self-correct) |

## <a name="recreate-a-missing-service-principal-with-powershell"></a>Recréer un principal de service manquant avec PowerShell
Suivez ces étapes si un principal de service avec l’ID ```2565bd9d-da50-47d4-8b85-4c97f669dc36``` est absent de votre annuaire Azure AD.

**Correction :** Vous avez besoin d’Azure AD PowerShell pour effectuer ces étapes. Pour plus d’informations sur l’installation d’Azure AD PowerShell, consultez [cet article](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0.).

Pour résoudre ce problème, saisissez les commandes suivantes dans une fenêtre PowerShell :
1. Installez le module Azure AD PowerShell, puis importez-le.
    
    ```powershell 
    Install-Module AzureAD
    Import-Module AzureAD
    ```
    
2. Saisissez la commande PowerShell suivante pour vérifier si le principal du service requis pour Azure AD Domain Services est absent de votre annuaire Azure AD :
    
    ```powershell
    Get-AzureAdServicePrincipal -filter "AppId eq '2565bd9d-da50-47d4-8b85-4c97f669dc36'"
    ```
    
3. Créez le principal de service en saisissant la commande PowerShell suivante :

    ```powershell
    New-AzureAdServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
    ```
    
4. Après avoir créé le principal de service manquant, attendez deux heures, puis vérifiez l’intégrité de votre domaine managé.


## <a name="re-register-to-the-microsoft-aad-namespace-using-the-azure-portal"></a>Réinscrivez l’espace de noms Microsoft AAD via le portail Azure
Suivez ces étapes si un principal de service avec l’ID ```443155a6-77f3-45e3-882b-22b3a8d431fb``` ou ```abba844e-bc0e-44b0-947a-dc74e5d09022``` est absent de votre annuaire Azure AD.

**Correction :** Pour restaurer les services de domaine dans votre annuaire, effectuez les étapes suivantes :

1. Accédez à la page [Abonnements](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) dans le portail Azure.
2. Choisissez l’abonnement dans la table qui est associée à votre domaine géré
3. À l’aide de la navigation de gauche, choisissez **Fournisseurs de ressources**
4. Recherchez « Microsoft.AAD » dans la table et cliquez sur **Réinscrire**
5. Pour vérifier que l’alerte est résolue, attendez deux heures, puis consultez la page de contrôle d’intégrité de votre domaine managé.


## <a name="service-principals-that-self-correct"></a>Principaux de service avec correction autonome
Suivez ces étapes si un principal de service avec l’ID ```d87dcbc6-a371-462e-88e3-28ad15ec4e64``` est absent de votre annuaire Azure AD.

**Correction :** Azure AD Domain Services peut détecter à quel moment le principal de service est devenu manquant ou mal configuré, ou à quel moment il a été supprimé. Le service recrée automatiquement ce principal de service. Vérifiez l’intégrité de votre domaine managé au bout de deux heures pour être sûr que le principal de service a été recréé.


## <a name="contact-us"></a>Nous contacter
Contactez l’équipe produit des Services de domaine Azure Active Directory pour [partager vos commentaires ou pour obtenir de l’aide](active-directory-ds-contact-us.md).
