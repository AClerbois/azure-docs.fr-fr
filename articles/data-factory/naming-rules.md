---
title: Règles d’affectation des noms des entités Azure Data Factory | Microsoft Docs
description: Décrit les règles d'affectation de noms pour les entités Data Factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: bc5e801d-0b3b-48ec-9501-bb4146ea17f1
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: shlo
ms.openlocfilehash: e9d2140edc64daca4df0463dbfdcd5b06f072012
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34620406"
---
# <a name="azure-data-factory---naming-rules"></a>Azure Data Factory - Règles d’affectation des noms
Le tableau suivant fournit des règles d'affectation de noms pour les artefacts Data Factory.

> [!NOTE]
> Cet article s’applique à la version 2 de Data Factory, actuellement en préversion. Si vous utilisez la version 1 du service Data Factory, qui est généralement disponible, consultez [Règles de nommage dans Data Factory version 1](v1/data-factory-naming-rules.md).

| NOM | Unicité du nom | Contrôles de validation |
|:--- |:--- |:--- |
| Data Factory |Unique sur Microsoft Azure. Les noms ne respectent pas la casse, c’est-à-dire que `MyDF` et `mydf` font référence à la même fabrique de données. |<ul><li>Chaque fabrique de données est liée à un seul abonnement Azure.</li><li>Les noms d’objet doivent commencer par une lettre ou un chiffre, et peuvent comporter uniquement des lettres, des chiffres et des tirets (-).</li><li>Chaque tiret (-) doit être immédiatement précédé et suivi par une lettre ou un chiffre. Les tirets consécutifs ne sont pas autorisés dans les noms de conteneurs.</li><li>Le nom doit contenir entre 3 et 63 caractères.</li></ul> |
| Services/tableaux/pipelines liés |Unique dans une fabrique de données. Les noms sont sensibles à la casse. |<ul><li>Un tableau peut contenir un maximum de 260 caractères.</li><li>Les noms d’objet doivent commencer par une lettre, un chiffre ou un trait de soulignement (_).</li><li>Les caractères suivants ne sont pas autorisés : « . », « + », « ? », « / », « < », « > », « * », « % », « & », « : », « \\ »</li><li>Les tirets (« - ») ne sont pas autorisés dans les noms de services liés et de jeux de données uniquement.</li></ul>  |
| Groupe de ressources |Unique sur Microsoft Azure. Les noms sont sensibles à la casse. | Pour plus d’informations, consultez la page [Règles d’affectation de noms Azure et restrictions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions#naming-rules-and-restrictions). |

## <a name="next-steps"></a>Étapes suivantes
Apprenez à créer des fabriques de données en suivant les instructions étape par étape de l’article [Démarrage rapide : créer une fabrique de données](quickstart-create-data-factory-powershell.md). 
