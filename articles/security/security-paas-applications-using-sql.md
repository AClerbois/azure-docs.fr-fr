---
title: Sécurisation des bases de données PaaS dans Azure | Microsoft Docs
description: 'Découvrez les bonnes pratiques de sécurité Azure SQL Database et SQL Data Warehouse pour protéger vos applications mobiles et web PaaS. '
services: security
documentationcenter: na
author: techlake
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/28/2018
ms.author: terrylan
ms.openlocfilehash: 22db43413b5c752decf6785a75dff22ff4a68039
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60596673"
---
# <a name="best-practices-for-securing-paas-databases-in-azure"></a>Bonnes pratiques pour la sécurisation des bases de données PaaS dans Azure

Dans cet article, nous abordons un ensemble de bonnes pratiques de sécurité [Azure SQL Database](../sql-database/sql-database-technical-overview.md) et [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) pour protéger vos applications mobiles et web PaaS (Platform-as-a-Service). Ces bonnes pratiques sont issues de notre expérience d’Azure, mais également de celle des clients, comme vous.

Azure SQL Database et SQL Data Warehouse fournissent un service de base de données relationnelle pour vos applications basées sur Internet. Examinons les services qui protègent vos applications et vos données lors de l’utilisation de SQL Database et de SQL Data Warehouse dans un déploiement PaaS :

- Authentification Azure Active Directory (au lieu de l'authentification SQL Server)
- Pare-feu SQL Azure
- Chiffrement transparent des données (TDE)

## <a name="use-a-centralized-identity-repository"></a>Utiliser un référentiel d’identités centralisé
Des bases de données SQL Azure peuvent être configurées pour utiliser l'un des deux types d’authentification :

- **L’authentification SQL** utilise un nom d’utilisateur et un mot de passe. Lorsque vous avez créé un serveur logique pour votre base de données, vous avez spécifié un compte de connexion « Admin serveur », associé à un nom d’utilisateur et à un mot de passe. À l’aide de ces informations d’identification, vous pouvez vous authentifier auprès de n’importe quelle base de données sur ce serveur en tant que propriétaire de la base de données.

- **L’authentification Azure Active Directory** utilise des identités gérées par Azure Active Directory et est prise en charge pour les domaines managés et intégrés. Pour utiliser l’authentification Azure Active Directory, vous devez créer un autre administrateur de serveur appelé « administrateur Azure AD », autorisé à gérer les groupes et utilisateurs Active Directory Azure. Cet administrateur peut également effectuer toutes les opérations d’un administrateur de serveur ordinaire.

[L’authentification Azure Active Directory](../active-directory/develop/authentication-scenarios.md) est un mécanisme servant à se connecter aux services Azure SQL Database et SQL Data Warehouse à l’aide d’identités dans Azure Active Directory (Azure AD). Azure AD fournit une alternative à l’authentification SQL Server. Vous pouvez donc arrêter la prolifération des identités d’utilisateur sur les serveurs de base de données. L’authentification Azure AD vous permet de gérer de manière centralisée les identités des utilisateurs de base de données et d’autres services Microsoft dans un emplacement centralisé. La gestion centralisée des ID fournit un emplacement unique pour gérer les utilisateurs de la base de données et simplifie la gestion des autorisations.  

### <a name="benefits-of-using-azure-ad-instead-of-sql-authentication"></a>Avantages de l’utilisation de l’authentification Azure AD plutôt que l’authentification SQL
- Permet une rotation du mot de passe dans un emplacement unique.
- Gère des autorisations de base de données à l'aide de groupes Azure AD externes.
- Élimine le stockage des mots de passe en activant l’authentification Windows intégrée et d'autres formes d’authentification prises en charge par Azure AD.
- Utilise les utilisateurs de base de données autonome pour authentifier les identités au niveau de la base de données.
- Prend en charge l’authentification basée sur les jetons pour les applications se connectant à SQL Database.
- Prend en charge la fédération de domaine avec Services ADFS (Active Directory Federation Services) ou l’authentification utilisateur natif/mot de passe pour un répertoire Azure AD local sans synchronisation du domaine.
- Prend en charge les connexions à partir de SQL Server Management Studio qui utilisent l’authentification universelle Active Directory, notamment [Multi-Factor Authentication (MFA)](../active-directory/authentication/multi-factor-authentication.md). MFA comprend une authentification forte avec une gamme d’options de vérification simples (appel téléphonique, SMS, cartes à puce avec code PIN ou notification d’application mobile). Pour plus d’informations, consultez [Authentification universelle avec SQL Database et SQL Data Warehouse](../sql-database/sql-database-ssms-mfa-authentication.md).

Pour en savoir plus sur l'authentification Azure AD, consultez :

- [Utiliser l’authentification Azure Active Directory pour l’authentification auprès de SQL Database, de Managed Instance ou de SQL Data Warehouse](../sql-database/sql-database-aad-authentication.md)
- [Authentification sur Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-authentication.md)
- [L’authentification basée sur le jeton prend en charge la base de données SQL Azure à l’aide de l’authentification Azure AD](../sql-database/sql-database-aad-authentication.md)

> [!NOTE]
> Pour garantir qu’Azure Active Directory est adapté à votre environnement, consultez [Limitations et fonctionnalités Azure AD](../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations).
>
>

## <a name="restrict-access-based-on-ip-address"></a>Restreindre l’accès en fonction de l’adresse IP
Vous pouvez créer des règles de pare-feu qui spécifient des plages d’adresses IP acceptables. Ces règles peuvent être ciblées au niveau du serveur et de la base de données. Nous recommandons d’utiliser, dans la mesure du possible, des règles de pare-feu au niveau de la base de données pour améliorer la sécurité et renforcer la portabilité de la base de données. L'utilisation de règles de pare-feu pour les administrateurs est recommandée au niveau du serveur quand plusieurs bases de données ont les mêmes exigences d’accès alors que vous ne souhaitez pas les configurer une à une.

Les restrictions d’adresse IP source par défaut de SQL Database autorisent l’accès à partir de n’importe quelle adresse Azure, notamment d’autres abonnements et locataires. Vous pouvez limiter cette option pour autoriser uniquement vos adresses IP pour accéder à l’instance. Même avec votre pare-feu SQL et les restrictions d’adresse IP, l’authentification forte est toujours nécessaire. Consultez les recommandations faites plus haut dans cet article.

Pour en savoir plus sur le pare-feu SQL Azure et les restrictions d'adresse IP, consultez :

- [Contrôle de l’accès à Azure SQL Database et SQL Data Warehouse](../sql-database/sql-database-control-access.md)
- [Règles de pare-feu Azure SQL Database et SQL Data Warehouse](../sql-database/sql-database-firewall-configure.md)


## <a name="encrypt-data-at-rest"></a>Chiffrer des données au repos
L’option [Transparent Data Encryption (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption) est activée par défaut. TDE chiffre de manière transparente les fichiers journaux et les données SQL Server, Azure SQL Database et Azure SQL Data Warehouse. L’option TDE empêche la compromission d’un accès direct aux fichiers ou leur sauvegarde. Cela vous permet de chiffrer les données au repos sans modifier les applications existantes. L’option TDE doit toujours être activée. Cependant, cela n’empêchera pas un pirate informatique d’utiliser le chemin d’accès normal. TDE permet de se conformer aux multiples lois, réglementations et directives établies dans de nombreux secteurs.

Azure SQL gère les problèmes clés liés à TDE. Comme avec TDE, une attention particulière doit être portée au niveau local pour la capacité de restauration et le déplacement des bases de données. Dans des scénarios plus complexes, les clés peuvent être explicitement gérées dans Azure Key Vault par le biais de la gestion de clés extensible. Consultez [Activer TDE sur SQL Server à l’aide d’EKM](/sql/relational-databases/security/encryption/enable-tde-on-sql-server-using-ekm). Cela permet également d’utiliser la méthode BYOK (Bring Your Own Key) au moyen de la fonctionnalité Azure Key Vault BYOK.

Azure SQL fournit un chiffrement pour les colonnes par le biais d’[Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine). Cela permet de restreindre l’accès des colonnes sensibles aux applications autorisées. Ce chiffrement limite les requêtes SQL pour les colonnes chiffrées aux valeurs basées sur l’égalité.

Le chiffrement au niveau de l’application doit également être utilisé pour des données sélectionnées. Les problèmes de souveraineté de données peuvent être atténués par le chiffrement des données avec une clé conservée dans le pays qui convient. Cela empêche même tout problème dû à un transfert de données accidentel, car il est impossible de déchiffrer les données sans la clé, en supposant qu’un algorithme fort soit utilisé (par exemple, AES 256).

Vous pouvez prendre des précautions supplémentaires pour sécuriser la base de données, comme la conception d’un système sécurisé, le chiffrement de ressources confidentielles et la création d’un pare-feu autour des serveurs de base de données.

## <a name="next-steps"></a>Étapes suivantes
Dans cet article, nous avons abordé un ensemble de bonnes pratiques de sécurité SQL Database et SQL Data Warehouse pour protéger vos applications PaaS mobiles et web. Pour en savoir plus sur la sécurisation de vos déploiements PaaS, consultez :

- [Sécurisation des déploiements PaaS](security-paas-deployments.md)
- [Sécurisation des applications mobiles et web PaaS à l’aide d’Azure App Services](security-paas-applications-using-app-services.md)
