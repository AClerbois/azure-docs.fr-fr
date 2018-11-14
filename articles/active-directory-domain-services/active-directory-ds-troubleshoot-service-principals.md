---
title: 'Azure Active Directory Domain Services : Dépannage de la configuration du principal de service | Microsoft Docs'
description: Résolution des problèmes de configuration du principal de service pour les services de domaine Azure AD
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: ''
editor: ''
ms.assetid: f168870c-b43a-4dd6-a13f-5cfadc5edf2c
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ergreenl
ms.openlocfilehash: bba7c70a5078d309a55f898c24389d42a8a604ab
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2018
ms.locfileid: "51035033"
---
# <a name="troubleshoot-invalid-service-principal-configuration-for-your-managed-domain"></a>Résoudre les problèmes liés à une configuration de principal de service non valide pour votre domaine managé

Cet article vous aide à résoudre les erreurs liées à la configuration du principal de service qui entraînent l’affichage du message d’alerte suivant :

## <a name="alert-aadds102-service-principal-not-found"></a>Alerte AADDS102 : Principal de service introuvable

**Message d’alerte :**  *Un principal du service requis pour qu’Azure AD Domain Services fonctionne correctement a été supprimé de votre locataire Azure AD. Cette configuration affecte la capacité de Microsoft à surveiller, gérer, mettre à jour et synchroniser votre domaine géré.*

Les [principaux de service](../active-directory/develop/app-objects-and-service-principals.md) sont des applications que Microsoft utilise pour gérer, mettre à jour et maintenir votre domaine managé. S’ils sont supprimés, ils interrompent la capacité de Microsoft à travailler sur votre domaine.


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
| d87dcbc6-a371-462e-88e3-28ad15ec4e64 | [Réinscrire l’espace de noms Microsoft.AAD](#re-register-to-the-microsoft-aad-namespace-using-the-azure-portal) |

## <a name="recreate-a-missing-service-principal-with-powershell"></a>Recréer un principal de service manquant avec PowerShell
Suivez ces étapes si un principal de service avec l’ID ```2565bd9d-da50-47d4-8b85-4c97f669dc36``` est absent de votre annuaire Azure AD.

**Résolution :** vous avez besoin d’Azure AD PowerShell pour effectuer ces étapes. Pour plus d’informations sur l’installation d’Azure AD PowerShell, consultez [cet article](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0.).

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
Suivez ces étapes s’il manque un principal de service avec l’ID ```443155a6-77f3-45e3-882b-22b3a8d431fb```, ```abba844e-bc0e-44b0-947a-dc74e5d09022``` ou ```d87dcbc6-a371-462e-88e3-28ad15ec4e64``` dans votre annuaire Azure AD.

**Résolution :** procédez comme suit pour restaurer Domain Services dans votre annuaire :

1. Accédez à la page [Abonnements](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) dans le portail Azure.
2. Choisissez l’abonnement dans la table qui est associée à votre domaine géré
3. À l’aide de la navigation de gauche, choisissez **Fournisseurs de ressources**
4. Recherchez « Microsoft.AAD » dans la table et cliquez sur **Réinscrire**
5. Pour vérifier que l’alerte est résolue, attendez deux heures, puis consultez la page de contrôle d’intégrité de votre domaine managé.


## <a name="alert-aadds105-password-synchronization-application-is-out-of-date"></a>Alerte AADDS105 : l’application de synchronisation du mot de passe est obsolète

**Message d’alerte :** le principal du service avec l’application ID « d87dcbc6-a371-462e-88e3-28ad15ec4e64 » a été supprimé puis recréé. Cette nouvelle création laisse des autorisations incohérentes sur les ressources Azure AD Domain Services nécessaires pour traiter votre domaine managé. La synchronisation des mots de passe dans votre domaine managé pourrait en être affectée.


**Résolution :** vous avez besoin d’Azure AD PowerShell pour effectuer ces étapes. Pour plus d’informations sur l’installation d’Azure AD PowerShell, consultez [cet article](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0.).

Pour résoudre ce problème, saisissez les commandes suivantes dans une fenêtre PowerShell :
1. Installez le module Azure AD PowerShell, puis importez-le.

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```
2. Supprimer l’ancienne application et l’ancien objet à l’aide des commandes PowerShell suivantes

    ```powershell
    $app = Get-AzureADApplication -Filter "IdentifierUris eq 'https://sync.aaddc.activedirectory.windowsazure.com'"
    Remove-AzureADApplication -ObjectId $app.ObjectId
    $spObject = Get-AzureADServicePrincipal -Filter "DisplayName eq 'Azure AD Domain Services Sync'"
    Remove-AzureADServicePrincipal -ObjectId $app.ObjectId
    ```
3. Après avoir supprimé les deux, le système se corrige de lui-même et recrée les applications nécessaires à la synchronisation du mot de passe. Pour garantir que l’alerte a été corrigée, attendez deux heures et vérifiez l’intégrité de votre domaine.


## <a name="contact-us"></a>Nous contacter
Contactez l’équipe produit des Services de domaine Azure Active Directory pour [partager vos commentaires ou pour obtenir de l’aide](active-directory-ds-contact-us.md).
