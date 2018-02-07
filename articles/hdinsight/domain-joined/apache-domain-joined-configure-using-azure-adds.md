---
title: "Configurer les clusters HDInsight joints à un domaine avec Azure Active Directory Domain Services - Azure | Microsoft Docs"
description: "Découvrez comment configurer les clusters HDInsight joints à un domaine à l’aide d’Azure Active Directory Domain Services."
services: hdinsight
documentationcenter: 
author: bprakash
manager: jhubbard
editor: cgronlun
tags: 
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/10/2017
ms.author: bhanupr
ms.openlocfilehash: 77478616eae27828a57a36dc0aaf3884e80ce403
ms.sourcegitcommit: 28178ca0364e498318e2630f51ba6158e4a09a89
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
# <a name="configure-domain-joined-hdinsight-clusters-using-azure-active-directory-domain-services"></a>Configurer les clusters HDInsight joints à un domaine à l’aide d’Azure Active Directory Domain Services

Les clusters joints à un domaine fournissent des fonctionnalités de sécurité d’entreprise multi-utilisateur dans HDInsight. Les clusters HDInsight joints à un domaine sont connectés à des domaines Active Directory. De cette façon, les utilisateurs du domaine peuvent utiliser leurs informations d’identification de domaine pour s’authentifier auprès des clusters et exécuter des tâches de Big Data. 

Il existe deux façons de configurer un contrôleur de domaine pour qu’un cluster HDInsight joint à un domaine puisse se connecter à :

- Azure Active Directory Domain Services (Azure AD DS)
- Un contrôleur de domaine Active Directory sur des machines virtuelles Azure IaaS

Dans cet article, vous allez voir comment configurer un cluster HDInsight joint à un domaine à l’aide d’Azure Active Directory Domain Services.

## <a name="create-azure-adds"></a>Créer une instance d’Azure AD DS

Vous devez créer une instance d’Azure AD DS avant de pouvoir créer un cluster HDInsight. Pour plus d’informations sur la création d’une instance d’Azure ADDS, consultez [Activer Azure Active Directory Domain Services à l’aide du portail Azure](../../active-directory-domain-services/active-directory-ds-getting-started.md). 

> [!NOTE]
> Seuls les administrateurs des locataires disposent des autorisations pour créer une instance d’Azure AD DS. Si vous utilisez Azure Data Lake Storage (ADLS) comme stockage par défaut pour HDInsight, vérifiez que le locataire Azure AD par défaut pour ADLS est identique au domaine pour le cluster HDInsight. 

Une fois que le service de domaine a été provisionné, vous devez créer un compte de service dans le groupe **Administrateurs AAD DC** pour créer le cluster HDInsight. Le compte de service doit être celui d’un administrateur général dans Azure AD.

## <a name="create-a-domain-joined-hdinsight-cluster"></a>Créer un cluster HDInsight joint à un domaine

L’étape suivante consiste à créer le cluster HDInsight à l’aide d’AAD DS et du compte de service créé dans la section précédente.

Il est plus facile de placer AAD DS et le cluster HDInsight dans le même réseau virtuel Azure. Si ce n’est pas le cas, vous devez appairer les deux réseaux virtuels. Pour en savoir plus, consultez [Homologation de réseaux virtuels](../../virtual-network/virtual-network-peering-overview.md).

Lorsque vous créez un cluster HDInsight joint à un domaine, vous devez fournir les paramètres suivants :

- **Nom de domaine** : nom de domaine associé à l’instance d’Azure AD DS. Par exemple, contoso.onmicrosoft.com.
- **Nom d’utilisateur du domaine** : compte de service appartenant au groupe Administrateurs AAD DC Azure qui a été créé dans la section précédente. Par exemple : hdiadmin@contoso.onmicrosoft.com. Cet utilisateur de domaine est l’administrateur du cluster HDInsight joint à un domaine.
- **Mot de passe du domaine** : mot de passe du compte de service.
- **Unité de l’organisation** : nom unique de l’unité d’organisation à utiliser avec le cluster HDInsight. Par exemple : OU=HDInsightOU,DC=contoso,DC=onmicrosohift,DC=com. Si cette unité d’organisation n’existe pas, le cluster HDInsight tente de la créer. 
- **URL LDAPS** : par exemple, ldaps://contoso.onmicrosoft.com:636
- **Accès au groupe d’utilisateurs** : groupes de sécurité dont vous souhaitez synchroniser les utilisateurs avec le cluster. Par exemple, HiveUsers. Si vous souhaitez spécifier plusieurs groupes d’utilisateurs, séparez-les par des virgules (« , »).
 
La capture d’écran suivante montre les configurations dans le portail Azure :

![Configuration d’un cluster HDInsight joint à un domaine avec Active Directory Domain Services](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-domain-joined-configuration-azure-aads-portal.png).


## <a name="next-steps"></a>étapes suivantes
* Pour configurer des stratégies Hive et exécuter des requêtes Hive, consultez [Configuration de stratégies Hive pour les clusters HDInsight joints à un domaine](apache-domain-joined-run-hive.md).
* Pour utiliser des clusters HDInsight joints à un domaine, consultez [Utilisation de SSH avec Hadoop sous Linux sur HDInsight à partir de Linux, Unix ou OS X](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).

