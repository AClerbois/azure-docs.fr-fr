---
title: Indexation de documents dans Azure Data Lake Storage Gen2 (préversion)
titleSuffix: Azure Cognitive Search
description: Découvrez comment indexer le contenu et les métadonnées dans Azure Data Lake Storage Gen2. Cette fonctionnalité est actuellement disponible en préversion publique
manager: nitinme
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 390f45ce874e4e64d3a7b9a6c3944983e0ba9028
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73719918"
---
# <a name="indexing-documents-in-azure-data-lake-storage-gen2"></a>Indexation de documents dans Azure Data Lake Storage Gen2

> [!IMPORTANT] 
> La prise en charge d’Azure Data Lake Storage Gen2 est actuellement en préversion publique. Les fonctionnalités en préversion sont fournies sans contrat de niveau de service et ne sont pas recommandées pour les charges de travail de production. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Vous pouvez demander l’accès aux préversions en remplissant [ce formulaire](https://aka.ms/azure-cognitive-search/indexer-preview). L’[API REST version 2019-05-06-Preview](search-api-preview.md) fournit cette fonctionnalité. Il n’y a actuellement pas de prise en charge du portail ou du SDK .NET.


Lorsque vous configurez un compte de stockage Azure, vous avez la possibilité d’activer un [espace de noms hiérarchique](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-namespace). Cela permet d’organiser la collection du contenu d’un compte dans une hiérarchie de répertoires et de sous-répertoires imbriqués. En activant l’espace de noms hiérarchique, vous activez [Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction).

Cet article explique comment prendre en main l’indexation des documents qui se trouvent dans Azure Data Lake Storage Gen2.

## <a name="set-up-azure-data-lake-storage-gen2-indexer"></a>Configurer l’indexeur Azure Data Lake Storage Gen2

Vous devez effectuer quelques étapes pour indexer le contenu de Data Lake Storage Gen2.

### <a name="step-1-sign-up-for-the-preview"></a>Étape 1 : S’inscrire à la version préliminaire

Inscrivez-vous à la préversion de l’indexeur Data Lake Storage Gen2 en remplissant [ce formulaire](https://aka.ms/azure-cognitive-search/indexer-preview). Vous recevrez un e-mail de confirmation une fois que vous avez été accepté dans la préversion.

### <a name="step-2-follow-the-azure-blob-storage-indexing-setup-steps"></a>Étape 2 : Suivre les étapes de configuration de l’indexation du stockage d’objets blob Azure

Une fois que vous avez reçu la confirmation de la réussite de votre l’inscription à la préversion, vous êtes prêt à créer le pipeline d’indexation.

Vous pouvez indexer le contenu et les métadonnées de Data Lake Storage Gen2 à l’aide de l’[’API REST version 2019-05-06-Preview](search-api-preview.md). Il n’y a pas de prise en charge de portail ou de SDK .NET pour l’instant.

L’indexation du contenu dans Data Lake Storage Gen2 est identique à l’indexation du contenu dans le stockage d’objets Blob Azure. Pour savoir comment configurer la source de données, l’index et l’indexeur Data Lake Storage Gen2, consultez [Comment indexer des documents dans Stockage Blob Azure avec la Recherche cognitive Azure](search-howto-indexing-azure-blob-storage.md). L’article relatif au stockage d’objets Blob fournit également des informations sur les formats de document pris en charge, les propriétés de métadonnées d’objet Blob extraites, l’indexation incrémentielle, et bien d’autres encore. Ces informations seront identiques pour Data Lake Storage Gen2.

## <a name="access-control"></a>Contrôle d’accès

Azure Data Lake Storage Gen2 implémente un [modèle de contrôle d’accès](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control) qui prend en charge le contrôle d’accès en fonction du rôle (RBAC) Azure et les listes de contrôle d’accès (ACL) POSIX. Lorsque vous indexez du contenu à partir de Data Lake Storage Gen2, Recherche cognitive Azure n’extraira pas les informations RBAC et ACL du contenu. Par conséquent, ces informations ne seront pas incluses dans votre index Recherche cognitive Azure.

Si la gestion du contrôle d’accès sur chaque document de l’index est importante, le développeur de l’application doit implémenter un [filtrage de sécurité](https://docs.microsoft.com/azure/search/search-security-trimming-for-azure-search).