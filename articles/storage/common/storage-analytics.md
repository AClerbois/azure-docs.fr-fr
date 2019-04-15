---
title: Utiliser Azure Storage Analytics pour collecter les journaux d’activité et les données de mesure | Microsoft Docs
description: Storage Analytics vous permet d’une part d’effectuer le suivi des données de métriques pour tous les services de stockage et, d’autre part, de collecter les journaux d’activité de Blob Storage, Queue Storage et Table Storage.
services: storage
author: roygara
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 03/03/2017
ms.author: rogarana
ms.subservice: common
ms.openlocfilehash: eb85f8c756e7373a8dedabbce362cfa534e56fd8
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57849174"
---
# <a name="storage-analytics"></a>Storage Analytics

Azure Storage Analytics effectue la journalisation et fournit des données de métriques pour un compte de stockage. Vous pouvez utiliser ces données pour suivre les demandes, analyser les tendances d'utilisation et diagnostiquer les problèmes liés à votre compte de stockage

Pour utiliser Storage Analytics, vous devez l'activer individuellement pour chaque service que vous souhaitez analyser. Vous pouvez l’activer à partir de la [Azure portal](https://portal.azure.com). Pour plus d’informations, consultez [surveiller un compte de stockage dans le portail Azure](storage-monitor-storage-account.md). Vous pouvez également activer Storage Analytics par programmation via l'API REST ou la bibliothèque cliente. Utilisez le [Set Blob Service Properties](/rest/api/storageservices/set-blob-service-properties), [Set Queue Service Properties](/rest/api/storageservices/set-queue-service-properties), [Set Table Service Properties](/rest/api/storageservices/set-table-service-properties), et [définir les propriétés du Service fichier](/rest/api/storageservices/Get-File-Service-Properties)opérations pour activer l’Analytique de stockage pour chaque service.

Les données agrégées sont stockées dans un objet blob connu (pour la journalisation) et dans des tables connues (pour les métriques), qui sont accessibles via les API du service BLOB et du service de Table.

Storage Analytics a une limite de 20 To pour la quantité de données stockées qui est indépendante de la limite totale pour votre compte de stockage. Pour plus d'informations sur les limites des comptes de stockage, consultez la page [Objectifs d'évolutivité et de performances d'Azure Storage](storage-scalability-targets.md).

Pour obtenir un guide détaillé concernant l'utilisation de Storage Analytics et d'autres outils permettant d'analyser, de diagnostiquer et de résoudre les problèmes d'Azure Storage, consultez [Analyse, diagnostic et résolution des problèmes rencontrés sur Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="billing-for-storage-analytics"></a>Facturation pour Storage Analytics

Toutes les données de métriques sont écrites par les services d'un compte de stockage. En conséquence, chaque opération d'écriture effectuée par Storage Analytics est facturable. En outre, la quantité de stockage utilisée par les données de métriques est également facturable.

Les actions suivantes effectuées par Storage Analytics sont facturables :

* Demandes de création d'objets blob pour la journalisation.
* Demandes de création d'entités de table pour les métriques.

Si vous avez configuré une stratégie de rétention des données, vous n'êtes pas facturé pour les transactions de suppression lorsque Storage Analytics supprime les anciennes données de journalisation et de métriques. Toutefois, les transactions de suppression d'un client sont facturables. Pour plus d'informations sur les stratégies de rétention, consultez [Définition d'une stratégie de rétention des données Storage Analytics](https://msdn.microsoft.com/library/azure/hh343263.aspx).

### <a name="understanding-billable-requests"></a>Présentation des demandes facturables

Chaque demande adressée à un service de stockage d’un compte est facturable ou non. Storage Analytics enregistre chaque demande effectuée à un service, avec un message d’état qui indique comment la demande a été traitée. De même, Storage Analytics stocke des métriques pour un service et pour les opérations de l'API de ce service, y compris les pourcentages et le décompte de certains messages d'état. Conjointement, ces fonctionnalités peuvent vous aider à analyser vos demandes facturables, à améliorer votre application et à diagnostiquer les problèmes liés aux demandes à vos services. Pour plus d'informations sur la facturation, consultez [Présentation de la facturation d’Azure Storage - bande passante, transactions et capacité](https://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx).

Lorsque vous consultez des données Storage Analytics, vous pouvez utiliser les tables de la rubrique [Opérations et messages d'état enregistrés Storage Analytics](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) pour identifier les demandes facturables. Vous pouvez ensuite comparer vos données de journaux d’activité et de métriques aux messages d’état pour déterminer si vous avez été facturé pour une demande spécifique. Vous pouvez également utiliser les tables de la rubrique précédente pour examiner la disponibilité d'un service de stockage ou d'une opération individuelle de l'API.

## <a name="next-steps"></a>Étapes suivantes
* [Surveiller un compte de stockage dans le portail Azure](storage-monitor-storage-account.md)
* [Métriques de Storage Analytics](storage-analytics-metrics.md)
* [Journalisation Storage Analytics](storage-analytics-logging.md)
