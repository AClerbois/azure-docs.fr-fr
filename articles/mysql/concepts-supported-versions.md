---
title: Versions prises en charge dans Azure Database pour MySQL
description: Décrit les versions prises en charge des bases de données Azure pour MySQL.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 05/23/2018
ms.openlocfilehash: c9a533ed9b9eb9ac53a02439b98a78954c7aaa11
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35265246"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>Versions prises en charge du serveur de base de données Azure pour MySQL
Azure Database for MySQL a été développé à partir de [MySQL Community Edition](https://www.mysql.com/products/community/), avec le moteur InnoDB.  Azure Database pour MySQL prend actuellement en charge les versions suivantes :

## <a name="mysql-version-5639"></a>MySQL version 5.6.39
Consultez la [documentation](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-39.html) MySQL pour en savoir plus sur les améliorations et les correctifs introduits dans MySQL 5.6.39.

## <a name="mysql-version-5721"></a>MySQL version 5.7.21
Consultez la [documentation](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-21.html) MySQL pour en savoir plus sur les améliorations et les correctifs introduits dans MySQL 5.7.21.

> [!NOTE]
> Dans le service, une passerelle est utilisée pour rediriger les connexions vers des instances de serveur. Une fois la connexion établie, le client de MySQL affiche la version de MySQL définie dans la passerelle, et non la version en cours d’exécution sur votre instance de serveur MySQL. Pour déterminer la version de votre instance de serveur MySQL, utilisez la commande `SELECT VERSION();` à l’invite de MySQL. 

## <a name="managing-updates-and-upgrades"></a>Gestion des mises à jour et des mises à niveau
Le service gère automatiquement les correctifs pour les mises à jour de version mineures. Les mises à niveau de version principales ne sont pas prises en charge (par ex. la mise à niveau à partir de MySQL 5.6 pour MySQL 5.7).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les quotas de ressources et les limitations associés à votre **niveau de service**, consultez la page [Niveaux de service](./concepts-pricing-tiers.md).
