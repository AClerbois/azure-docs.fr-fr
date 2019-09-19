---
title: Exporter ou importer votre configuration de provisionnement à l’aide de l’API Graph | Microsoft Docs
description: Découvrez comment exporter et importer une configuration de provisionnement à l’aide de l’API Graph.
services: active-directory
author: cmmdesai
documentationcenter: na
manager: daveba
ms.assetid: 1a2c375a-1bb1-4a61-8115-5a69972c6ad6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/09/2019
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1c3e92ee5ffd97174331703b703e811bd1ce5f43
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2019
ms.locfileid: "70815850"
---
# <a name="export-or-import-your-provisioning-configuration-by-using-graph-api"></a>Exporter ou importer votre configuration de provisionnement à l’aide de l’API Graph

Vous pouvez utiliser l’API Microsoft Graph et l’Afficheur Graph pour exporter vos mappages d’attributs et votre schéma de provisionnement utilisateur dans un fichier JSON et le réimporter dans Azure AD. Vous pouvez aussi utiliser les étapes capturées ici pour créer une sauvegarde de votre configuration de provisionnement. 

## <a name="step-1-retrieve-your-provisioning-app-service-principal-id-object-id"></a>Étape 1 : Récupérer l’ID de principal du service de l’application de provisionnement (ID d’objet)

1. Lancez le [portail Azure](https://portal.azure.com) et accédez à la section Propriétés de votre application de provisionnement. Par exemple, si vous souhaitez exporter votre mappage d’*application de provisionnement de Workday vers AD*, accédez à la section Propriétés de cette application. 
1. Dans la section Propriétés de votre application d'approvisionnement, copiez la valeur GUID associée au champ *ID de l'objet*. Cette valeur, également appelée **ServicePrincipalId** de votre application, sera utilisée dans les opérations de l'Afficheur Graph.

   ![ID du principal de service de l'application Workday](./media/export-import-provisioning-mappings/wd_export_01.png)

## <a name="step-2-sign-into-microsoft-graph-explorer"></a>Étape 2 : Se connecter à l'Afficheur Microsoft Graph

1. Lancez l'[Afficheur Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer).
1. Cliquez sur le bouton « Se connecter avec Microsoft » et connectez-vous à l'aide des informations d'identification d'administrateur de l'application ou d'administrateur global d'Azure AD.

    ![Connexion à Graph](./media/export-import-provisioning-mappings/wd_export_02.png)

1. Une fois connecté, les détails du compte d'utilisateur apparaissent dans le volet de gauche.

## <a name="step-3-retrieve-the-provisioning-job-id-of-the-provisioning-app"></a>Étape 3 : Récupérer l’ID de travail de provisionnement de l’application de provisionnement

Dans l'Afficheur Microsoft Graph, exécutez la requête GET suivante en remplaçant [servicePrincipalId] par la valeur **ServicePrincipalId** extraite à l'[étape 1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id).

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs
```

Vous obtiendrez une réponse semblable à l'exemple ci-dessous. Copier l'attribut « id » présent dans la réponse. Il s'agit de la valeur **ProvisioningJobId** qui sera utilisée pour extraire les métadonnées du schéma sous-jacent.

   [![ID du travail d’approvisionnement](./media/export-import-provisioning-mappings/wd_export_03.png)](./media/export-import-provisioning-mappings/wd_export_03.png#lightbox)

## <a name="step-4-download-the-provisioning-schema"></a>Étape 4 : Télécharger le schéma d'approvisionnement

Dans l'Afficheur Microsoft Graph, exécutez la requête GET suivante, en remplaçant [servicePrincipalId] et [ProvisioningJobId] par les valeurs ServicePrincipalId et ProvisioningJobId extraites lors des étapes précédentes.

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

Copiez l'objet JSON à partir de la réponse et enregistrez-le dans un fichier pour créer une sauvegarde du schéma.

## <a name="step-5-import-the-provisioning-schema"></a>Étape 5 : Importer le schéma d'approvisionnement

> [!CAUTION]
> Ne suivez cette étape que si vous devez modifier le schéma pour une configuration non modifiable à l'aide du portail Azure ou si vous devez restaurer la configuration à partir d'un fichier précédemment sauvegardé et contenant un schéma valide et fonctionnel.

Dans l'Afficheur Microsoft Graph, configurez la requête PUT suivante en remplaçant [servicePrincipalId] et [ProvisioningJobId] par les valeurs ServicePrincipalId et ProvisioningJobId extraites lors des étapes précédentes.

```http
    PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

Dans l'onglet « Corps de la demande », copiez le contenu du fichier de schéma JSON.

   [![Corps de la demande](./media/export-import-provisioning-mappings/wd_export_04.png)](./media/export-import-provisioning-mappings/wd_export_04.png#lightbox)

Sous l'onglet « En-têtes des demandes », ajoutez l'attribut d'en-tête Content-Type avec la valeur « application/json ».

   [![En-têtes des demandes](./media/export-import-provisioning-mappings/wd_export_05.png)](./media/export-import-provisioning-mappings/wd_export_05.png#lightbox)

Cliquez sur le bouton « Exécuter la requête » pour importer le nouveau schéma.
