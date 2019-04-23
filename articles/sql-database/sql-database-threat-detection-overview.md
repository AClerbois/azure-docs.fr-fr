---
title: Protection avancée contre les menaces - Azure SQL Database | Microsoft Docs
description: Advanced Threat Protection détecte les activités de base de données anormales indiquant des menaces de sécurité potentielles dans la base de données SQL Azure.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: monhaber
ms.author: ronmat
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 03/31/2019
ms.openlocfilehash: 710a94c919f4262c3f572f28d03c79b77e658287
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59797062"
---
# <a name="advanced-threat-protection-for-azure-sql-database"></a>Protection avancée contre les menaces pour Azure SQL Database

Advanced Threat Protection pour [base de données SQL Azure](sql-database-technical-overview.md) et [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) détecte les activités anormales indiquant des tentatives inhabituelles ou potentiellement dangereuses visant à accéder ou à exploiter les bases de données.

Advanced Threat Protection fait partie de la [fonctions avancées de sécurité des données](sql-database-advanced-data-security.md) (ADS) offre, qui est un package unifié incluant des fonctionnalités de sécurité avancées SQL. Advanced Threat Protection peuvent être accessibles et gérée via un portail central des annonces de SQL.

> [!NOTE]
> Cette rubrique s’applique à un serveur SQL Azure et aux bases de données SQL Database et SQL Data Warehouse créées sur le serveur SQL Azure. Par souci de simplicité, la base de données SQL est utilisée pour faire référence à SQL Database et SQL Data Warehouse.

## <a name="what-is-advanced-threat-protection"></a>Nouveautés d’Advanced Threat Protection

 Advanced Threat Protection fournit une nouvelle couche de sécurité, ce qui permet aux clients de détecter et répondre aux menaces potentielles qu’elles se présentent en générant des alertes de sécurité sur les activités anormales. Les utilisateurs reçoivent une alerte en cas d’activités de base de données suspectes, de vulnérabilités potentielles, d’attaques par injection de code SQL et de modèles d’accès et de requêtes anormaux à la base de données. Advanced Threat Protection intègre les alertes avec [Azure Security Center](https://azure.microsoft.com/services/security-center/), qui incluent des détails sur les activités suspectes et recommandent l’action sur la façon d’examiner et atténuer la menace. Advanced Threat Protection facile à gérer les menaces potentielles à la base de données sans avoir à être un expert en sécurité ou gérer des systèmes de surveillance de sécurité avancée.

Pour une expérience d’analyse complète, il est recommandé d’activer [SQL Database Auditing](sql-database-auditing.md), qui écrit les événements de la base de données dans un journal d’audit sur votre compte de stockage Azure.  

## <a name="advanced-threat-protection-alerts"></a>Alertes Azure ATP

Advanced Threat Protection pour Azure SQL Database détecte les activités anormales indiquant des tentatives inhabituelles ou potentiellement dangereuses visant à accéder ou à exploiter les bases de données et il peut déclencher les alertes suivantes :

- **Vulnérabilité par injection de code SQL** : cette alerte est déclenchée quand une application génère une instruction SQL défectueuse dans la base de données. Cette alerte peut éventuellement indiquer une vulnérabilité aux attaques par injection de code SQL. Deux raisons possibles expliquent la génération d’une instruction défectueuse :

  - Un défaut dans le code d’application qui crée l’instruction SQL défectueuse
  - Le code de l’application ou des procédures stockées qui n’assainissent pas l’entrée utilisateur lors de la création de l’instruction SQL défectueuse, qui peut être exploitée par les injections SQL
- **Injection potentielle de code SQL** : cette alerte est déclenchée en cas d’attaque active contre une vulnérabilité d’application identifiée sur l’injection SQL. Cela signifie que l’attaquant tente d’injecter des instructions SQL malveillantes en utilisant les procédures stockées ou le code d’application vulnérables.
- **Accès à partir d’un emplacement inhabituel** : cette alerte est déclenchée en cas de changement du modèle d’accès au serveur SQL, quand un utilisateur s’est connecté au serveur SQL à partir d’un emplacement géographique inhabituel. Dans certains cas, l’alerte détecte une action légitime (une nouvelle application ou opération de maintenance du développeur). Dans d’autres cas, l’alerte détecte une action malveillante (ancien employé, attaquant externe).
- **Accès à partir d’un centre de données Azure inhabituel** : cette alerte est déclenchée en cas de changement du modèle d’accès au serveur SQL, quand un utilisateur s’est connecté au serveur SQL à partir d’un centre de données Azure inhabituel observé sur ce serveur récemment. Dans certains cas, l’alerte détecte une action légitime (votre nouvelle application dans Azure, Power BI, l’éditeur de requête SQL Azure). Dans d’autres cas, l’alerte détecte une action malveillante provenant d’une ressource/d’un service Azure (ancien employé, attaquant externe).
- **Accès à partir d’une entité de sécurité inconnue** : cette alerte est déclenchée en cas de modification du modèle d’accès au serveur SQL, quand un utilisateur s’est connecté au serveur SQL à l’aide d’un principal inhabituel (utilisateur SQL). Dans certains cas, l’alerte détecte une action légitime (nouvelle application, opération de maintenance du développeur). Dans d’autres cas, l’alerte détecte une action malveillante (ancien employé, attaquant externe).
- **Accès à partir d’une application potentiellement dangereuse** : cette alerte est déclenchée quand une application potentiellement dangereuse est utilisée pour accéder à la base de données. Dans certains cas, l’alerte détecte le test d’intrusion en action. Dans d’autres cas, l’alerte détecte une attaque à l’aide d’outils d’attaque courants.
- **Informations d’identification SQL par force brute** : cette alerte est déclenchée quand il existe un nombre anormalement élevé d’échecs de connexion avec des informations d’identification différentes. Dans certains cas, l’alerte détecte le test d’intrusion en action. Dans d’autres cas, l’alerte détecte une attaque par force brute.

## <a name="explore-anomalous-database-activities-upon-detection-of-a-suspicious-event"></a>Explorer les activités anormales sur la base de données en cas de détection d’un événement suspect

Vous recevez une notification par e-mail quand des activités anormales sont détectées sur la base de données. L’e-mail contient des informations sur l’événement de sécurité suspect, notamment la nature des activités anormales, le nom de la base de données, le nom du serveur, le nom de l’application et l’heure de l’événement. Il fournit également des informations sur les causes possibles et les mesures recommandées pour examiner et atténuer la menace potentielle pesant sur la base de données.

![Rapports d’activités anormales](./media/sql-database-threat-detection/anomalous_activity_report.png)

1. Cliquez sur le lien **Afficher les alertes SQL récentes** contenu dans l’e-mail pour ouvrir le portail Azure et accéder à la page d’alertes d’Azure Security Center, qui fournit une vue d’ensemble des menaces actives détectées sur la base de données SQL.

   ![Menaces d’activité](./media/sql-database-threat-detection/active_threats.png)

2. Cliquez sur une alerte spécifique pour obtenir des détails supplémentaires et des actions permettant d’examiner cette menace et d’atténuer les menaces futures.

   Par exemple, l’injection SQL représente l’un des problèmes de sécurité les plus courants auxquels sont le plus exposées les applications web, et est utilisée pour cibler les applications pilotées par des données. Les pirates exploitent les vulnérabilités des applications pour injecter des instructions SQL nuisibles dans les champs de saisie d’application afin de violer ou modifier les données contenues dans la base de données. Pour les alertes d’injection SQL, les détails de l’alerte incluent l’instruction SQL vulnérable qui a été exploitée.

   ![Alerte spécifique](./media/sql-database-threat-detection/specific_alert.png)

## <a name="explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal"></a>Explorez les alertes-Protection avancée contre les menaces pour votre base de données dans le portail Azure

Advanced Threat Protection intègre ses alertes avec [centre de sécurité Azure](https://azure.microsoft.com/services/security-center/). Les vignettes dynamiques SQL-Protection avancée contre les menaces au sein de la base de données et les panneaux de publicités de SQL dans le portail Azure suivre l’état des menaces actives.

Cliquez sur **alerte d’Advanced Threat Protection** pour lancer le centre de sécurité Azure alertes page et obtenir une vue d’ensemble des menaces SQL actives détectées sur l’entrepôt de données ou de la base de données.

   ![Alerte de Protection contre les menaces avancée](./media/sql-database-threat-detection/threat_detection_alert.png)

   ![Advanced Threat Protection alert2](./media/sql-database-threat-detection/threat_detection_alert_atp.png)

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [avancées de Protection contre les menaces dans les bases de données uniques et bureaux](sql-database-threat-detection.md).
- En savoir plus sur [Advanced Threat Protection dans l’instance managée](sql-database-managed-instance-threat-detection.md).
- En savoir plus sur [fonctions avancées de sécurité des données](sql-database-advanced-data-security.md).
- En savoir plus sur [Audit Azure SQL Database](sql-database-auditing.md)
- En savoir plus sur [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
- Pour plus d'informations sur la tarification, consultez la [page de tarification SQL Database](https://azure.microsoft.com/pricing/details/sql-database/)  
