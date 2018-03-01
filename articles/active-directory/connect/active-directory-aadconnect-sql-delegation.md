---
title: "Installer Azure AD Connect à l’aide d’autorisations administrateur déléguées SQL | Microsoft Docs"
description: "Cette rubrique concerne une mise à jour Azure AD Connect qui permet une installation à l’aide d’un compte disposant uniquement d’autorisations de propriétaire de base de données SQL."
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.reviewer: jparsons
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2018
ms.author: billmath
ms.openlocfilehash: c2d77c37f2f65c9a7db1fd5c4010fc43bcbc7ebf
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="install-azure-ad-connect-using-sql-delegated-administrator-permissions"></a>Installer Azure AD Connect à l’aide d’autorisations administrateur déléguées SQL
Avant la dernière version d’Azure AD Connect, la délégation administrative n’était pas prise en charge lors du déploiement de configurations nécessitant SQL.  Les utilisateurs qui souhaitent installer Azure AD Connect devaient disposer d’autorisations d’administrateur de serveur sur le serveur SQL.

Avec la version la plus récente d’Azure AD Connect, le provisionnement de la base de données peut désormais être exécuté hors bande par l’administrateur SQL. L’installation s’effectue ensuite par l’administrateur Azure AD Connect disposant de droits de propriétaire de base de données.

## <a name="before-you-begin"></a>Avant de commencer
Pour utiliser cette fonctionnalité, vous devez savoir qu’il existe plusieurs éléments, et que chacun d’eux peut impliquer un administrateur différent.  Le tableau suivant récapitule les rôle et leurs fonctions respectives lors du déploiement d’Azure AD Connect avec cette fonctionnalité.

|Rôle|DESCRIPTION|
|-----|-----|
|Administrateur AD de domaine ou de forêt|Crée le compte de service au niveau du domaine, qui est utilisé par Azure AD Connect pour exécuter le service de synchronisation.  Pour plus d’informations, consultez [Comptes et autorisations](active-directory-aadconnect-accounts-permissions.md).
|Administrateur SQL|Crée la base de données ADSync et accorde l’accès Connexion + Propriétaire de base de données à l’administrateur Azure AD Connect et au compte de service créé par l’administrateur du domaine ou de la forêt.|
Administrateur Azure AD Connect|Installe Azure AD Connect et spécifie le compte de service pendant l’installation personnalisée.

## <a name="steps-for-installing-azure-ad-connect-using-sql-delegated-permissions"></a>Étapes d’installation d’Azure AD Connect à l’aide d’autorisations déléguées SQL
Pour provisionner la base de données hors bande et installer Azure AD Connect avec des autorisations de propriétaire de base de données, suivez les étapes ci-dessous.

>[!NOTE]
>Même si cela n’est pas obligatoire, il est **fortement recommandé** de sélectionner le classement Latin1_General_CI_AS lorsque vous créez une base de données.


1.  Demandez à l’administrateur SQL de créer la base de données ADSync avec une séquence de classement insensible à la casse **(Latin1_General_CI_AS)**.  La base de données doit être nommée **ADSync**.  Le mode de récupération, le niveau de compatibilité et le type de relation contenant-contenu sont mis à jour avec les valeurs appropriées lors de l’installation d’Azure AD Connect.  Toutefois, la séquence de classement doit être définie correctement par l’administrateur SQL. Dans le cas contraire, Azure AD Connect va bloquer l’installation.  Pour effectuer une récupération, l’administrateur de base de données doit supprimer et recréer la base de données.</br>
![Classement](media/active-directory-aadconnect-sql-delegation/sql1.png)
2.  Accordez les autorisations suivantes à l’administrateur Azure AD Connect et au compte de service de domaine :
    - Connexion SQL 
    - Droits de **propriétaire de base de données (dbo)**  </br>
![autorisations](media/active-directory-aadconnect-sql-delegation/sql3.png)
3.  Envoyez un e-mail à l’administrateur Azure AD Connect en indiquant le serveur SQL et le nom de l’instance qui doivent être utilisés lors de l’installation d’Azure AD Connect.

## <a name="additional-information"></a>Informations supplémentaires
Une fois que la base de données est provisionnée, l’administrateur Azure AD Connect peut installer et configurer la synchronisation localement, à sa convenance.  

En plus de prendre en charge les nouvelles installations d’Azure AD Connect, cette fonctionnalité permet également la délégation pour tous les scénarios liés à l’indicateur **/UseExistingDatabase**.  Pour plus d’informations sur l’installation d’Azure AD Connect avec une base de données existante, consultez [Installer Azure AD Connect à l’aide d’une base de données ADSync existante](active-directory-aadconnect-existing-database.md).


## <a name="next-steps"></a>étapes suivantes
- [Prise en main d’Azure AD Connect à l’aide de paramètres express](active-directory-aadconnect-get-started-express.md)
- [Installation personnalisée d’Azure AD Connect](active-directory-aadconnect-get-started-custom.md)
- [Installer Azure AD Connect à l’aide d’une base de données ADSync existante](active-directory-aadconnect-existing-database.md)  