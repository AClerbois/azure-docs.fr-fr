---
title: Vue d’ensemble de la continuité d’activité avec Azure Database pour PostgreSQL
description: Vue d’ensemble de la continuité d’activité avec Azure Database pour PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: c8c62f6a80cf06e3444c4c3efd9979e723dcf488
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/18/2018
ms.locfileid: "53559040"
---
# <a name="overview-of-business-continuity-with-azure-database-for-postgresql"></a>Vue d’ensemble de la continuité d’activité avec Azure Database pour PostgreSQL

Cette vue d’ensemble décrit les fonctionnalités de continuité d’activité et de reprise après sinistre que fournit Azure Database pour PostgreSQL. Découvrez les options de reprise à la suite d’événements d’interruption susceptibles d’entraîner une perte de données ou une indisponibilité de votre base de données et de votre application. Connaissez la procédure à suivre lorsqu’un utilisateur ou qu’une erreur d’application affecte l’intégrité des données, lorsqu’une région Azure subit une panne ou que votre application nécessite une maintenance.

## <a name="features-that-you-can-use-to-provide-business-continuity"></a>Fonctionnalités que vous pouvez utiliser pour garantir la continuité d’activité

Azure Database pour PostgreSQL propose des fonctionnalités de continuité d’activité, notamment des sauvegardes automatisées et la possibilité pour les utilisateurs de lancer une géorestauration. Chacune de ces fonctionnalités présente des caractéristiques spécifiques concernant le temps de récupération estimé (ERT) et le risque de perte de données. Une fois que vous avez compris ces options, vous pouvez choisir celles qui vous conviennent et les utiliser ensemble dans différents scénarios. Au moment d’élaborer votre plan de continuité d’activité, vous devez comprendre le délai maximal acceptable nécessaire à la récupération complète de l’application après l’événement d’interruption, c’est-à-dire votre objectif de délai de récupération (RTO). Vous devez aussi comprendre la quantité maximale des récentes mises à jour de données (intervalle) que l’application peut accepter de perdre lors de la reprise après l’événement d’interruption, c’est-à-dire votre objectif de point de récupération (RPO).

Le tableau suivant compare l’ERT et le RPO pour les fonctionnalités disponibles :

| **Fonctionnalité** | **De base** | **Usage général** | **Mémoire optimisée** |
| :------------: | :-------: | :-----------------: | :------------------: |
| Limite de restauration dans le temps à partir de la sauvegarde | N’importe quel point de restauration dans la période de rétention | N’importe quel point de restauration dans la période de rétention | N’importe quel point de restauration dans la période de rétention |
| Géo-restauration à partir de sauvegardes répliquées géographiquement | Non pris en charge | ERT < 12 h<br/>RPO < 1 h | ERT < 12 h<br/>RPO < 1 h |

> [!IMPORTANT]
> Il n’est **pas** possible de restaurer des serveurs supprimés. Si vous supprimez le serveur, toutes les bases de données qui appartiennent au serveur sont également supprimées, sans pouvoir être restaurées.

## <a name="recover-a-server-after-a-user-or-application-error"></a>Récupérer un serveur après une erreur d’utilisateur ou d’application

Vous pouvez vous servir des sauvegardes du service pour récupérer un serveur à la suite de plusieurs événements d’interruption. Un utilisateur peut supprimer par inadvertance des données, une table importante, voire une base de données tout entière. Une application peut accidentellement remplacer des données correctes par des données incorrectes en raison d’une défaillance d’application, etc.

Vous pouvez procéder à une restauration jusqu’à une date et heure pour créer une copie de votre serveur à un moment donné adéquat et connu. Ce moment donné doit se situer dans la période de rétention de sauvegarde que vous avez configurée pour votre serveur. Une fois les données restaurées sur le nouveau serveur, vous pouvez remplacer le serveur d’origine par le serveur nouvellement restauré ou copier les données nécessaires du serveur restauré sur le serveur d’origine.

## <a name="recover-from-an-azure-regional-data-center-outage"></a>Récupérer à la suite d’une panne du centre de données régional Azure

Bien que le fait soit rare, un centre de données Azure peut subir une panne. En cas de panne, il s’ensuit une interruption d’activité dont la durée peut se limiter à quelques minutes, mais aussi se compter en heures.

Vous pouvez attendre que votre serveur redevienne disponible une fois la panne réparée au niveau du centre de données. Cette solution vaut pour les applications qui peuvent s’accommoder d’un certain temps d’indisponibilité du serveur, par exemple dans un environnement de développement. Quand un centre de données subit une panne, vous ne savez pas combien de temps cela peut durer. Cette solution n’est donc valable que si vous n’avez pas besoin du serveur pendant un certain temps.

L’autre solution consiste à utiliser la fonctionnalité de géorestauration d’Azure Database pour PostgreSQL qui restaure le serveur à partir de sauvegardes géoredondantes. Ces sauvegardes sont accessibles même si la région dans laquelle votre serveur est hébergé n’est pas disponible. Vous pouvez effectuer une restauration à partir de ces sauvegardes dans n’importe quelle autre région et remettre votre serveur en ligne.

> [!IMPORTANT]
> La géorestauration n’est possible que si vous avez provisionné le serveur avec le stockage de sauvegardes géoredondantes. Si vous voulez passer des sauvegardes redondantes localement à des sauvegardes géoredondantes pour un serveur existant, vous devez prendre une image mémoire de votre serveur existant avec pg_dump et la restaurer sur une configuration nouvellement créée avec des sauvegardes géoredondantes.

## <a name="next-steps"></a>Étapes suivantes
- Pour plus d’informations sur les sauvegardes automatisées, consultez [Sauvegardes dans Azure Database pour PostgreSQL](concepts-backup.md). 
- Pour effectuer une restauration à un moment donné à partir du portail Azure, consultez  [Restaurer une base de données à un moment donné à partir du portail Azure](howto-restore-server-portal.md).
- Pour effectuer une restauration à un point dans le temps à l’aide de l’interface de ligne de commande Azure, consultez  [Restaurer une base de données à un point dans le temps à l’aide de l’interface CLI](howto-restore-server-cli.md).