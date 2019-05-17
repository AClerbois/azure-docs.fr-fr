---
title: Utiliser un cache externe dans Gestion des API Azure | Microsoft Docs
description: Découvrez comment configurer et utiliser un cache externe dans Gestion des API Azure.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: erikre
editor: ''
ms.assetid: 740f6a27-8323-474d-ade2-828ae0c75e7a
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: apimpm
ms.openlocfilehash: e2362d06fa0ef795122a2d47a7a621b66fdd9470
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65780356"
---
# <a name="use-an-external-azure-cache-for-redis-in-azure-api-management"></a>Utiliser un Cache Azure pour Redis externe dans Gestion des API Azure

En plus de l’utilisation du cache intégré, Gestion des API Azure permet également la mise en cache de réponses dans un Cache Azure pour Redis externe.

L’utilisation d’un cache externe permet de pallier quelques limites du cache intégré. Il est particulièrement utile si vous souhaitez :

* Éviter que votre cache soit régulièrement effacé lors des mises à jour de Gestion des API
* Obtenir un plus grand contrôle sur la configuration de votre cache
* Mettre en cache plus de données que ce que votre niveau Gestion des API vous permet
* Utiliser la mise en cache avec le niveau Consommation de Gestion des API

Pour plus d’informations sur la mise en cache, consultez [Stratégies de mise en cache dans Gestion des API](api-management-caching-policies.md) et [Mise en cache personnalisée dans Gestion des API Azure](api-management-sample-cache-by-key.md).

![Apport de votre propre cache à APIM](media/api-management-howto-cache-external/overview.png)

Ce que vous allez apprendre :

> [!div class="checklist"]
> * Ajouter un cache externe dans Gestion des API

## <a name="prerequisites"></a>Conditions préalables

Pour suivre ce didacticiel, vous devez effectuer les opérations suivantes :

+ [Créer une instance du service Gestion des API Azure](get-started-create-service-instance.md)
+ Comprendre la [mise en cache dans Gestion des API Azure](api-management-howto-cache.md)

## <a name="create-cache"> </a> Créer un Cache Azure pour Redis

Cette section explique comment créer un Cache Azure pour Redis dans Azure. Si vous disposez déjà d’un Cache Azure pour Redis, dans ou en dehors d’Azure, vous pouvez <a href="#add-external-cache">passer</a> à la section suivante.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="add-external-cache"> </a> Ajouter un cache externe

Suivez les étapes ci-dessous pour ajouter un Cache Azure pour Redis externe dans Gestion des API Azure.

![Apport de votre propre cache à APIM](media/api-management-howto-cache-external/add-external-cache.png)

> [!NOTE]
> Le **utiliser à partir de** paramètre spécifie quels gestion des API déploiement régional communiqueront avec le cache configuré dans le cas d’une configuration de plusieurs région de la gestion des API. Les caches spécifiés **Par défaut** sont remplacés par les caches présentant une valeur régionale.
>
> Par exemple, si le service Gestion des API est hébergé dans les régions USA Est, Asie Sud-Est et Europe Ouest, et que deux caches sont configurés, l’un **Par défaut** et l’autre pour **Asie Sud-Est**, l’instance Gestion des API de la région **Asie Sud-Est** utilise son propre cache tandis que les deux autres régions utilisent l’entrée de cache **Par défaut**.

### <a name="add-an-azure-cache-for-redis-from-the-same-subscription"></a>Ajouter un Cache Azure pour Redis à partir du même abonnement

1. Accédez à votre instance Gestion des API dans le portail Azure.
2. Sélectionnez l’onglet **Cache externe** dans le menu de gauche.
3. Cliquez sur le bouton **+ Ajouter** .
4. Sélectionnez votre cache dans la liste déroulante **Instance de cache**.
5. Sélectionnez **par défaut** ou spécifier la région souhaitée dans le **utiliser à partir de** champ de liste déroulante.
6. Cliquez sur **Enregistrer**.

### <a name="add-an-azure-cache-for-redis-hosted-outside-of-the-current-azure-subscription-or-azure-in-general"></a>Ajouter un Cache Azure pour Redis hébergé en dehors de l’abonnement Azure actuel ou d’Azure en général

1. Accédez à votre instance Gestion des API dans le portail Azure.
2. Sélectionnez l’onglet **Cache externe** dans le menu de gauche.
3. Cliquez sur le bouton **+ Ajouter** .
4. Sélectionnez **Personnalisé** dans la zone de liste déroulante **Instance de cache**.
5. Sélectionnez **par défaut** ou spécifier la région souhaitée dans le **utiliser à partir de** champ de liste déroulante.
6. Indiquez la chaîne de connexion de votre Cache Azure pour Redis dans le champ **Chaîne de connexion**.
7. Cliquez sur **Enregistrer**.

## <a name="use-the-external-cache"></a>Utiliser le cache externe

Une fois le cache externe configuré dans Gestion des API Azure, il peut être utilisé par le biais des stratégies de mise en cache. Pour les instructions détaillées, consultez [Ajouter une mise en cache pour améliorer les performances de Gestion des API Azure](api-management-howto-cache.md).

## <a name="next-steps"> </a>Étapes suivantes

* Pour plus d’informations sur les stratégies de mise en cache, voir la section [Stratégies de mise en cache][Caching policies] dans [Référence de stratégie de Gestion des API][API Management policy reference].
* Pour plus d’informations sur la mise en cache des éléments par clé à l’aide d’expressions de stratégie, consultez [Mise en cache personnalisée dans la gestion des API Azure](api-management-sample-cache-by-key.md).

[API Management policy reference]: https://msdn.microsoft.com/library/azure/dn894081.aspx
[Caching policies]: https://msdn.microsoft.com/library/azure/dn894086.aspx
