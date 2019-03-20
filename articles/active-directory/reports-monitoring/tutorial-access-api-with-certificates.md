---
title: Didacticiel pour obtenir des données à l’aide de l’API Génération de rapports Azure AD avec des certificats | Microsoft Docs
description: Ce didacticiel explique comment utiliser l’API Génération de rapports Azure AD avec les informations d’identification de certificat pour obtenir des données à partir de répertoires sans intervention de l’utilisateur.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8706414b27969efd6aa26085370f8a0b1f891d69
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58095192"
---
# <a name="tutorial-get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>Tutoriel : Obtenir des données à l’aide de l’API de création de rapports Azure Active Directory avec des certificats

Les [API de création de rapports Azure Active Directory (Azure AD)](concept-reporting-api.md) vous fournissent un accès par programmation aux données par le biais d’un ensemble d’API REST. Vous pouvez appeler ces API à partir de divers outils et langages de programmation. Pour accéder à l’API de création de rapports Azure AD sans intervention de l’utilisateur, vous devez configurer votre accès afin d’utiliser des certificats.

Dans ce didacticiel, vous allez apprendre à utiliser un certificat de test pour accéder à l’API Graph MS pour générer des rapports. Nous vous recommandons de ne pas utiliser de certificats de test dans un environnement de production. 

## <a name="prerequisites"></a>Conditions préalables

1. Pour accéder aux données de connexion, vous devez disposer d'un locataire Azure Active Directory avec licence premium (P1/P2). Consultez [Bien démarrer avec Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) pour mettre à niveau votre édition d’Azure Active Directory. Notez que si vous ne disposiez d'aucune donnée d'activité avant la mise à niveau, quelques jours seront nécessaires pour que les données s'affichent dans les rapports après la mise à niveau vers une licence premium. 

2. Créez ou basculez vers un compte d'utilisateur dans le rôle **Administrateur général**, **Administrateur de la sécurité**, **Lecteur Sécurité** ou **Lecteur de rapport** pour le locataire. 

3. Remplissez les [conditions préalables pour accéder à l'API de création de rapports Azure Active Directory](howto-configure-prerequisites-for-reporting-api.md). 

4. Téléchargez et installez [Azure AD PowerShell V2](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/docs-conceptual/azureadps-2.0/install-adv2.md).

5. Installez [MSCloudIdUtils](https://www.powershellgallery.com/packages/MSCloudIdUtils/). Ce module fournit plusieurs applets de commande d’utilitaire, notamment :
    - Les bibliothèques d’authentification Active Directory (ADAL) nécessaires pour l’authentification
    - Les jetons d’accès d’utilisateur, de clés d’application et de certificats à l’aide d’ADAL
    - L’API Graph gérant les résultats paginés

6. S’il s’agit de la première utilisation du module, exécutez **Install-MSCloudIdUtilsModule**. Dans le cas contraire, importez-le à l’aide de la commande Powershell **Import-Module**. Votre session doit ressembler à ce qui suit : ![Windows Powershell](./media/tutorial-access-api-with-certificates/module-install.png)
  
7. Utilisez la cmdlet PowerShell **New-SelfSignedCertificate** pour créer un certificat de test.

   ```
   $cert = New-SelfSignedCertificate -Subject "CN=MSGraph_ReportingAPI" -CertStoreLocation "Cert:\CurrentUser\My" -KeyExportPolicy Exportable -KeySpec Signature -KeyLength 2048 -KeyAlgorithm RSA -HashAlgorithm SHA256
   ```

8. Utilisez la cmdlet **Export-Certificate** pour l’exporter dans un fichier de certificat.

   ```
   Export-Certificate -Cert $cert -FilePath "C:\Reporting\MSGraph_ReportingAPI.cer"

   ```

## <a name="get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>Obtenir des données à l’aide de l’API de création de rapports Azure Active Directory avec des certificats

1. Accédez au [portail Azure](https://portal.azure.com), sélectionnez **Azure Active Directory**, puis **Inscriptions des applications**, et choisissez votre application dans la liste. 

2. Sélectionnez **Paramètres** > **Clés**, puis **Télécharger la clé publique**.

3. Sélectionnez le fichier de certificat de l’étape précédente, puis **Enregistrer**. 

4. Notez l’ID de l’application et l’empreinte du certificat que vous venez d’enregistrer avec votre application. Pour rechercher l’empreinte, à partir de la page de votre application dans le portail, accédez à **Paramètres** et cliquez sur **Clés**. L’empreinte se trouve sous la liste **Clés publiques**.

5. Ouvrez le manifeste de l’application dans l’éditeur de manifeste en ligne et remplacez la propriété *keyCredentials* par les nouvelles informations de votre certificat, en utilisant le schéma suivant. 

   ```
   "keyCredentials": [
        {
            "customKeyIdentifier": "$base64Thumbprint", //base64 encoding of the certificate hash
            "keyId": "$keyid", //GUID to identify the key in the manifest
            "type": "AsymmetricX509Cert",
            "usage": "Verify",
            "value":  "$base64Value" //base64 encoding of the certificate raw data
        }
    ]
   ```

6. Enregistrez le manifeste. 
  
7. Maintenant, vous pouvez obtenir un jeton d’accès pour l’API Graph MS en utilisant ce certificat. Utilisez la cmdlet **Get-MSCloudIdMSGraphAccessTokenFromCert** du module PowerShell MSCloudIdUtils, en transmettant l’ID d’application et le thumbprint obtenus à l’étape précédente. 

   ![Portail Azure](./media/tutorial-access-api-with-certificates/getaccesstoken.png)

8. Utilisez le jeton d’accès dans votre script PowerShell pour interroger l’API Graph. Utilisez la cmdlet **Invoke-MSCloudIdMSGraphQuery** à partir de MSCloudIDUtils pour énumérer le point de terminaison SignIns et DirectoryAudits. Cette cmdlet gère les résultats composés de plusieurs pages et les envoie ensuite dans le pipeline PowerShell.

9. Interrogez le point de terminaison directoryAudits pour récupérer les journaux d’audit. 
   ![Portail Azure](./media/tutorial-access-api-with-certificates/query-directoryAudits.png)

10. Interrogez le point de terminaison SignIns pour récupérer les journaux de connexion.
    ![Portail Azure](./media/tutorial-access-api-with-certificates/query-signins.png)

11. Vous pouvez maintenant choisir d’exporter ces données dans un fichier CSV et de les enregistrer dans un système SIEM. Vous pouvez également encapsuler votre script dans une tâche planifiée pour obtenir régulièrement des données Azure AD à partir de votre client sans avoir à stocker des clés d’application dans le code source. 

## <a name="next-steps"></a>Étapes suivantes

* [Première impression des API de création de rapports](concept-reporting-api.md)
* [Informations de référence sur l’API d’audit](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [Informations de référence sur l’API de création de rapports relatifs à l’activité de connexion](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
