---
title: Sécuriser Azure Data Lake Analytics pour plusieurs utilisateurs
description: Découvrez comment configurer plusieurs utilisateurs pour exécuter des travaux dans Azure Data Lake Analytics.
ms.service: data-lake-analytics
services: data-lake-analytics
author: matt1883
ms.author: mahi
ms.reviewer: jasonwhowell
ms.topic: conceptual
ms.date: 05/30/2018
ms.openlocfilehash: 9fbc94259d6fdfb6758204efd6e6f0a346dc58da
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60813371"
---
# <a name="configure-user-access-to-job-information-to-job-information-in-azure-data-lake-analytics"></a>Configurer l’accès utilisateur aux informations sur les travaux dans Azure Data Lake Analytics 

Dans Azure Data Lake Analytics, vous pouvez utiliser plusieurs comptes d’utilisateur ou principaux de service pour exécuter des travaux. 

Afin que de pouvoir consulter les informations détaillées sur les travaux, les utilisateurs doivent être en mesure de lire le contenu des dossiers de travaux. Les dossiers de travaux se trouvent dans le répertoire `/system/`. 

Si les autorisations nécessaires ne sont pas configurées, l’utilisateur peut voir une erreur : `Graph data not available - You don't have permissions to access the graph data.` 

## <a name="configure-user-access-to-job-information"></a>Configurer l’accès utilisateur aux informations sur les travaux

Vous pouvez utiliser l’**Assistant Ajout d’un utilisateur** pour configurer les listes de contrôle d’accès sur les dossiers. Pour plus d’informations, consultez la rubrique [Ajouter un nouvel utilisateur](data-lake-analytics-manage-use-portal.md#add-a-new-user).

Si vous avez besoin d’un contrôle plus précis ou d’un script pour les autorisations, sécurisez les dossiers comme suit :

1. Octroyez des autorisations d’**exécution** (via une liste de contrôle d’accès) sur le dossier racine :
   - /
   
2. Octroyez des autorisations d’**exécution** et de **lecture** (via une liste de contrôle d’accès et une ACL par défaut) sur les dossiers qui contiennent les dossiers de travaux. Par exemple, pour un travail spécifique exécuté le 25 mai 2018, ces dossiers doivent être accessibles :
   - /system
   - /system/jobservice
   - /system/jobservice/jobs
   - /system/jobservice/jobs/Usql
   - /system/jobservice/jobs/Usql/2018
   - /system/jobservice/jobs/Usql/2018/05
   - /system/jobservice/jobs/Usql/2018/05/25
   - /system/jobservice/jobs/Usql/2018/05/25/11
   - /system/jobservice/jobs/Usql/2018/05/25/11/01
   - /system/jobservice/jobs/Usql/2018/05/25/11/01/b074bd7a-1448-d879-9d75-f562b101bd3d

## <a name="next-steps"></a>Étapes suivantes
[Ajouter un nouvel utilisateur](data-lake-analytics-manage-use-portal.md#add-a-new-user)
