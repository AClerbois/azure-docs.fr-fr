---
title: Azure Defender pour SQL
description: Apprenez-en plus sur les fonctionnalités permettant de gérer les vulnérabilités de votre base de données et de détecter les activités anormales pouvant indiquer une menace ciblant votre base de données dans Azure SQL Database, Azure SQL Managed Instance ou Azure Synapse.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.devlang: ''
ms.custom: sqldbrb=2
ms.topic: conceptual
ms.author: memildin
manager: rkarlin
author: memildin
ms.reviewer: vanto
ms.date: 09/21/2020
ms.openlocfilehash: b789cd423d2260ce709a02ffb3ac5ea500997609
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90930084"
---
# <a name="azure-defender-for-sql"></a>Azure Defender pour SQL
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]


Azure Defender pour SQL est un package unifié de fonctionnalités de sécurité SQL avancées. Azure Defender est disponible pour Azure SQL Database, Azure SQL Managed Instance et Azure Synapse Analytics. Il inclut des fonctionnalités permettant de découvrir et de classifier les données sensibles, de présenter et de corriger les vulnérabilités de votre base de données et de détecter les activités anormales susceptibles d’indiquer une menace ciblant votre base de données. Il vous permet d’activer et de gérer ces fonctionnalités à partir d’un seul et même emplacement.

## <a name="overview"></a>Vue d’ensemble

Azure Defender fournit un ensemble de fonctionnalités avancées de sécurité SQL, dont l’évaluation des vulnérabilités SQL et Advanced Threat Protection.
- La fonctionnalité [Évaluation des vulnérabilités](sql-vulnerability-assessment.md) est un service simple à configurer, qui vous permet de découvrir, suivre et corriger de potentielles vulnérabilités de base de données. Elle offre une visibilité sur votre état de sécurité et inclut des mesures pratiques pour résoudre les problèmes de sécurité et améliorer la protection de votre base de données.
- [Advanced Threat Protection](threat-detection-overview.md) détecte les activités anormales indiquant des tentatives d’accès ou d’exploitation inhabituelles et potentiellement dangereuses de votre base de données. Elle surveille en permanence votre base de données pour détecter des activités suspectes et envoie immédiatement des alertes de sécurité en cas de vulnérabilités éventuelles, d’attaques par injection de code SQL Azure et de modèles d’accès anormaux à la base de données. Les alertes Advanced Threat Protection fournissent des informations sur les activités suspectes et recommandent l’action à entreprendre pour analyser et atténuer la menace.

Activez Azure Defender pour SQL une seule fois pour activer toutes ces fonctionnalités incluses. D’un seul clic, vous pouvez activer Azure Defender pour toutes les bases de données sur votre [serveur](logical-servers.md) dans Azure ou dans votre instance gérée SQL. L’activation ou la gestion des paramètres Azure Defender nécessite d’appartenir au rôle [Gestionnaire de sécurité SQL](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-security-manager) ou à l’un des rôles d’administrateur de base de données ou de serveur.

Pour plus d’informations sur la tarification d’Azure Defender pour SQL, consultez la [page de tarification Azure Security Center](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="getting-started-with-azure-defender"></a>Prise en main d’Azure Defender

Les étapes suivantes vous aideront à démarrer avec Azure Defender.

## <a name="1-enable-azure-defender"></a>1. Activer Azure Defender

Azure Defender est accessible via le [portail Azure](https://portal.azure.com). Activez Azure Defender en accédant au **Centre de sécurité** sous l’en-tête **Sécurité** pour votre serveur ou instance gérée.

> [!NOTE]
> Un compte de stockage est automatiquement créé et configuré pour stocker les résultats d’analyse de votre **évaluation des vulnérabilités**. Si vous avez déjà activé Azure Defender pour un autre serveur dans le même groupe de ressources et la même région, alors le compte de stockage existant est utilisé.
>
> Le coût d’Azure Defender reflète la tarification par nœud du niveau Standard d’Azure Security Center, où un nœud correspond à l’intégralité du serveur ou de l’instance gérée. Vous payez donc une seule fois pour protéger toutes les bases de données sur le serveur ou l’instance gérée avec Azure Defender. Vous pouvez bénéficier d’un essai gratuit à Azure Defender.

## <a name="2-start-tracking-vulnerabilities-and-investigating-threat-alerts"></a>2. Démarrer le suivi des vulnérabilités et l’analyse des alertes de menaces

Cliquez sur la carte **Évaluation des vulnérabilités** pour afficher et gérer des rapports et des analyses de vulnérabilités et suivre votre état de sécurité. Si des alertes de sécurité ont été reçues, cliquez sur la carte **Advanced Threat Protection** pour afficher des détails des alertes et consulter un rapport regroupant toutes les alertes dans votre abonnement Azure par le biais de la page d’alertes de sécurité Azure Security Center.

## <a name="3-manage-azure-defender-settings"></a>3. Gérer les paramètres d’Azure Defender

Pour afficher et gérer les paramètres d’Azure Defender, accédez au **Centre de sécurité** sous l’en-tête **Sécurité** de votre serveur ou instance gérée. Dans cette page, vous pouvez activer ou désactiver Azure Defender et modifier les paramètres d’évaluation des vulnérabilités et d’Advanced Threat Protection pour l’intégralité de votre serveur ou de votre instance gérée.

## <a name="4-manage-azure-defender-settings-for-a-database"></a>4. Gérer les paramètres d’Azure Defender pour une base de données

Pour écraser les paramètres d’Azure Defender d’une base de données en particulier, cochez la case **Activer Azure Defender pour SQL au niveau de la base de données**. Utilisez cette option uniquement si vous avez besoin de recevoir des alertes Advanced Threat Protection ou des résultats d’évaluation des vulnérabilités distincts pour la base de données individuelle, au lieu ou en plus des alertes et résultats reçus pour toutes les bases de données présentes sur le serveur ou l’instance gérée.

Une fois que la case est cochée, vous pouvez configurer les paramètres appropriés pour cette base de données.

Les paramètres d’Azure Defender pour SQL de votre serveur ou instance gérée sont également accessibles depuis le volet de base de données Azure Defender. Cliquez sur **Paramètres** dans le volet principal Azure Defender, puis cliquez sur **Afficher les paramètres d’Azure Defender pour le serveur SQL**.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [Évaluation des vulnérabilités](sql-vulnerability-assessment.md)
- En savoir plus sur [Advanced Threat Protection](threat-detection-configure.md)
- En savoir plus sur [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
