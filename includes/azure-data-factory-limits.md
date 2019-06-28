---
title: Fichier Include
description: Fichier Include
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 5/30/2019
ms.author: jingwang
ms.custom: include file
ms.openlocfilehash: ac0747dcff4e74363a58dc9aaf6da4dbd4c6a1c7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66427579"
---
Azure Data Factory est un service mutualisé qui possède, par défaut, les limites suivantes pour garantir la protection des abonnements clients contre les autres charges de travail. Pour augmenter les limites jusqu'à la limite maximale pour votre abonnement, contactez le support.

### <a name="version-2"></a>version 2

| Ressource | Limite par défaut | Limite maximale |
| -------- | ------------- | ------------- |
| Fabriques de données d’un abonnement Azure | 50 | [Contacter le support](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Nombre total d’entités, telles que les pipeline, les jeux de données, les déclencheurs, les services liés et les runtimes d’intégration, dans une fabrique de données | 5 000 | [Contacter le support](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Nombre total de cœurs d’UC pour les runtimes d’intégration Azure-SSIS d’un même abonnement | 256 | [Contacter le support](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Exécutions de pipeline simultanés par fabrique de données qui sont partagées entre tous les pipelines de l’usine | 10 000  | [Contacter le support](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Les exécutions d’activités externes simultanées par abonnement par [région d’Azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)<br><small>Les activités externes sont gérées sur le runtime d’intégration, mais s’exécutent sur des services liés, notamment Databricks, une procédure stockée, HDInsights et autres.</small> | 3000 | [Contacter le support](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Les exécutions d’activités de pipeline simultanées par abonnement par [région d’Azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location) <br><small>Les activités de pipeline s’exécutent sur le runtime d’intégration, notamment la recherche, GetMetadata et la suppression. </small>| 1 000 | [Contacter le support](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Les opérations de création simultanées par abonnement pour chaque [région d’Azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)<br><small>Notamment tester la connexion, parcourir la liste des dossiers des tables, afficher un aperçu des données. | 200 | [Contacter le support](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Unités d’intégration simultanée de données<sup>1</sup> consommation par abonnement pour chaque [région Azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)| Groupe de régions 1<sup>2</sup> : 6000<br>Groupe de régions 2<sup>2</sup> : 3000<br>Groupe de régions 3<sup>2</sup> : 1 500 | [Contacter le support](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Activités maximales par pipeline, notamment les activités internes relatives aux conteneurs | 40 | 40 |
| Nombre maximal de runtime d'intégration liés pouvant être créés pour un même runtime d'intégration auto-hébergé | 100 | [Contacter le support](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Nombre maximal de paramètres par pipeline | 50 | 50 |
| Éléments ForEach | 100 000 | 100 000 |
| Parallélisme ForEach | 20 | 50 |
| Caractères par expression | 8 192 | 8 192 |
| Intervalle minimal de déclencheur de fenêtre bascule | 15 min | 15 min |
| Délai d’expiration maximal pour les exécutions d’activité de pipeline | 7 jours | 7 jours |
| Octets par objet pour les objets pipeline <sup>3</sup> | 200 Ko | 200 Ko |
| Octets par objet pour les objets jeu de données et service lié <sup>3</sup> | 100 Ko | 2 000 Ko |
| Unités d’intégration de données par exécution d’activité de copie <sup>1</sup> | 256 | [Contacter le support](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Écrire des appels d’API | 2 500/h<br/><br/> Cette limite est imposée par Azure Resource Manager, et non par Azure Data Factory. | [Contacter le support](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Lire des appels d’API | 12 500/h<br/><br/> Cette limite est imposée par Azure Resource Manager, et non par Azure Data Factory. | [Contacter le support](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Surveillance des requêtes par minute | 1 000 | [Contacter le support](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Opérations CRUD d’entité par minute | 50 | [Contacter le support](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |

<sup>1</sup> L’unité d’intégration de données (DIU) est utilisée dans une opération de copie de cloud à cloud, pour en savoir plus consultez [Unités d’intégration de données (version 2)](../articles/data-factory/copy-activity-performance.md#data-integration-units). Pour plus d’informations sur la facturation, consultez [Tarification d’Azure Data Factory](https://azure.microsoft.com/pricing/details/data-factory/).

<sup>2</sup> [Azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#azure-integration-runtime) est [disponible à l'échelle mondiale](https://azure.microsoft.com/global-infrastructure/services/) pour assurer la conformité des données, l'efficacité et des coûts de sortie réseau réduits. 

| Groupe de régions | Régions | 
| -------- | ------ |
| Groupe de régions 1 | USA Centre, USA Est, USA Est 2, Europe Nord, Europe Ouest, USA Ouest, USA Ouest 2 |
| Groupe de régions 2 | Australie Est, Australie Sud-Est, Brésil Sud, Inde Centre, Japon Est, USA Centre Nord, USA Centre Sud, Asie Sud-Est, USA Centre-Ouest |
| Groupe de régions 3 | Canada Centre, Asie Est, France Centre, Corée Centre, Royaume-Uni Sud |

<sup>3</sup> Les objets pipeline, jeu de données et service lié représentent un regroupement logique de votre charge de travail. Les limites de ces objets ne sont pas liées à la quantité de données que vous pouvez déplacer ou traiter à l’aide d’Azure Data Factory. Data Factory est conçu pour permettre une mise à l’échelle de plusieurs pétaoctets de données.

### <a name="version-1"></a>version 1

| **Ressource** | **Limite par défaut** | **Limite maximale** |
| --- | --- | --- |
| Fabriques de données d’un abonnement Azure |50 |[Contacter le support](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Pipelines dans une fabrique de données |2 500 |[Contacter le support](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Jeux de données dans une fabrique de données |5 000 |[Contacter le support](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Tranches simultanées par jeu de données |10 |10 |
| Octets par objet pour les objets pipeline<sup>1</sup> |200 Ko |200 Ko |
| Octets par objet pour les objets jeu de données et service lié<sup>1</sup> |100 Ko |2 000 Ko |
| Cœurs de cluster Azure HDInsight à la demande d’un abonnement<sup>2</sup> |60 |[Contacter le support](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Unités de déplacement de données cloud par exécution d’activité de copie<sup>3</sup> |32 |[Contacter le support](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Nombre de nouvelles tentatives pour les exécutions d’activités de pipeline |1 000 |MaxInt (32 bits) |

<sup>1</sup> Les objets pipeline, jeu de données et service lié représentent un regroupement logique de votre charge de travail. Les limites de ces objets ne sont pas liées à la quantité de données que vous pouvez déplacer ou traiter à l’aide d’Azure Data Factory. Data Factory est conçu pour permettre une mise à l’échelle de plusieurs pétaoctets de données.

<sup>2</sup> Les cœurs HDInsight à la demande sont alloués en dehors de l’abonnement qui contient la fabrique de données. Par conséquent, la limite précédente est la limite de cœurs d’appliquée à Data Factory pour les cœurs HDInsight à la demande. Elle est différente de la limite de cœurs qui est associée à votre abonnement Azure.

<sup>3</sup> L’unité de déplacement de données cloud (DMU) pour la version 1 est utilisée dans une opération de copie de cloud à cloud. Pour en savoir plus, consultez [Unités de déplacement de données (version 1) Cloud](../articles/data-factory/v1/data-factory-copy-activity-performance.md#cloud-data-movement-units). Pour plus d’informations sur la facturation, consultez [Tarification d’Azure Data Factory](https://azure.microsoft.com/pricing/details/data-factory/).

| **Ressource** | **Limite inférieure par défaut** | **Limite minimale** |
| --- | --- | --- |
| Intervalle de planification |15 minutes |15 minutes |
| Intervalle entre les nouvelles tentatives |1 seconde |1 seconde |
| Délai d’expiration des nouvelles tentatives |1 seconde |1 seconde |

#### <a name="web-service-call-limits"></a>Limites d’appels du service web
Azure Resource Manager fixe des limites aux appels d’API. Vous pouvez effectuer des appels d’API à une fréquence comprise dans les [limites d’API d’Azure Resource Manager](../articles/azure-subscription-service-limits.md#resource-group-limits).
