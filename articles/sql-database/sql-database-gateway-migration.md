---
title: Avis de migration du trafic de passerelle pour Azure SQL Database | Microsoft Docs
description: Cet article informe les utilisateurs de la migration des adresses IP des passerelles Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 07/01/2019
ms.openlocfilehash: 2e578c6bfdfb25eaba3d0c692ff33aa5b0572669
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70305993"
---
# <a name="azure-sql-database-traffic-migration-to-newer-gateways"></a>Migration du trafic des bases de données Azure SQL Database vers des passerelles plus récentes

Comme l’infrastructure Azure évolue, Microsoft actualise régulièrement le matériel, afin de proposer la meilleure expérience client possible. Dans les prochains mois, nous prévoyons d’ajouter des passerelles basées sur des générations de matériel plus récentes, de migrer le trafic vers ces passerelles et de mettre hors service les passerelles basées sur du matériel plus ancien dans certaines régions.  

Les clients seront informés très tôt de toute modification apportée aux passerelles disponibles dans chaque région, par e-mail et via le portail Microsoft Azure. Les informations les plus récentes seront conservées dans la table [Adresses IP des passerelles Azure SQL Database](sql-database-connectivity-architecture.md#azure-sql-database-gateway-ip-addresses).

## <a name="impact-of-this-change"></a>Portée de ce changement

Le premier cycle de migration du trafic vers les passerelles plus récentes est prévu pour le **14 octobre 2019** dans les régions suivantes :
- Brésil Sud
- USA Ouest
- Europe Ouest
- USA Est
- USA Centre
- Asie Sud-Est
- États-Unis - partie centrale méridionale
- Europe Nord
- Centre-Nord des États-Unis
- OuJapon Est
- Japon Est
- USA Est 2
- Asie Est

La migration du trafic change l’adresse IP publique résolue par DNS pour votre base de données SQL.
Vous êtes impacté dans les cas suivants :
- Vous avez codé en dur l’adresse IP d’une passerelle particulière dans votre pare-feu local.
- Vous avez des sous-réseaux qui utilisent Microsoft.SQL comme point de terminaison de service, mais vous ne pouvez pas communiquer avec les adresses IP de passerelle.

Vous n’êtes pas impacté dans les cas suivants :
- Vous utilisez la redirection comme stratégie de connexion.
- Vous avez des connexions à SQL Database à partir d’Azure et vous utilisez des balises de service.
- Il en va de même pour les connexions effectuées à l’aide des versions prises en charge de Microsoft JDBC Driver pour SQL Server. Pour les versions prises en charge du pilote JDBC, voir [Télécharger Microsoft JDBC Driver pour SQL Server](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server).

## <a name="what-to-do-you-do-if-youre-affected"></a>Que faire si vous êtes concerné

Nous vous recommandons d’autoriser le trafic sortant vers les adresses IP pour l’ensemble des [adresses IP de passerelles Azure SQL Database](sql-database-connectivity-architecture.md#azure-sql-database-gateway-ip-addresses) dans la région sur le port TCP 1433, et sur la plage de ports 11 000-11 999 pour votre pare-feu. Pour en savoir plus sur les plages de ports, voir [Stratégie de connexion](sql-database-connectivity-architecture.md#connection-policy).

La validation des certificats risque d’échouer pour les connexions établies à partir d’applications à l’aide d’une version de Microsoft JDBC Driver antérieure à 4.0. Les versions inférieures de Microsoft JDBC s’appuient sur un nom courant (CN) dans le champ d’objet du certificat. L’atténuation consiste à s’assurer que la propriété hostNameInCertificate est définie sur *.database.windows.net. Pour savoir comment définir la propriété hostNameInCertificate, voir [Connexion avec chiffrement SSL](/sql/connect/jdbc/connecting-with-ssl-encryption).

Si l’atténuation ci-dessus ne fonctionne pas, envoyez une demande de support concernant SQL Database, à l’aide de l’URL suivante : https://aka.ms/getazuresupport

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [l’architecture de la connectivité Azure SQL](sql-database-connectivity-architecture.md)