---
title: Authentifier l’accès aux objets blob et aux files d’attente Azure à l’aide d’Azure Active Directory | Microsoft Docs
description: Authentifiez l’accès aux objets blob et aux files d’attente Azure à l’aide d’Azure Active Directory.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/21/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 66051bd0f8be349f748c72218d538bba273be8f6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65147269"
---
# <a name="authenticate-access-to-azure-blobs-and-queues-using-azure-active-directory"></a>Authentifier l’accès aux objets blob et aux files d’attente Azure à l’aide d’Azure Active Directory

Le Stockage Azure prend en charge l’authentification et l’autorisation avec Azure Active Directory (AD) pour les services de Blob et de File d’attente. Avec Azure AD, vous pouvez utiliser le contrôle d’accès basé sur un rôle (RBAC) pour accorder l’accès aux utilisateurs, groupes ou principaux de service d’application. 

L’authentification des utilisateurs et des applications à l’aide des informations d’identification Azure AD est plus sécurisée et plus facile à utiliser que les autres modes d’autorisation. Même si vous pouvez continuer à utiliser l’autorisation de clé partagée avec vos applications, avec Azure AD, vous n’avez plus besoin de stocker votre clé d’accès de compte avec votre code. Vous pouvez également continuer à utiliser des signatures d’accès partagé (SAP) pour accorder un accès affiné aux ressources de votre compte de stockage. Toutefois, Azure AD offre des fonctionnalités similaires sans nécessiter de gestion des jetons SAP, ni de révocation des SAP compromises. Dans la mesure du possible, Microsoft recommande d’utiliser l’authentification Azure AD pour les applications Stockage Azure.

L’authentification et l’autorisation avec les informations d’identification Azure AD sont disponibles pour tous les comptes de stockage universels et comptes de stockage Blob dans la totalité des régions publiques et clouds nationaux. Seuls les comptes de stockage créés avec le modèle de déploiement Azure Resource Manager prennent en charge l’autorisation Azure AD.

## <a name="overview-of-azure-ad-for-blobs-and-queues"></a>Vue d’ensemble d’Azure AD pour les objets blob et les files d’attente

Lorsqu’un principal de sécurité (utilisateur, groupe ou application) tente d’accéder à une ressource d’objet blob ou de file d’attente, cette requête doit être autorisée, sauf s’il s’agit d’un objet blob disponible pour l’accès anonyme. Avec Azure AD, l’accès à une ressource est un processus en deux étapes. Pour commencer, l’identité du principal de sécurité est authentifiée, et un jeton OAuth 2.0 est renvoyé. Ensuite, ce jeton est transmis dans le cadre d’une requête adressée au service BLOB ou au service de File d’attente et utilisé par le service pour autoriser l’accès à la ressource spécifiée.

L’étape d’authentification nécessite qu’une application demande un jeton d’accès OAuth 2.0 au moment de l’exécution. Si une application s’exécute à partir d’une entité Azure telle qu’une machine virtuelle Azure, un groupe de machines virtuelles identiques ou une application Azure Functions, elle peut utiliser une [identité managée](../../active-directory/managed-identities-azure-resources/overview.md) pour accéder aux objets blob ou aux files d’attente. Pour plus d’informations sur l’autorisation des requêtes adressées par une identité managée aux services Azure BLOB ou de File d’attente, consultez l’article [Authenticate access to blobs and queues with Azure Active Directory and managed identities for Azure Resources](storage-auth-aad-msi.md) (Authentifier l’accès aux objets blob et files d’attente avec Azure Active Directory et les identités managées pour les ressources Azure).

L’étape d’autorisation exige qu’un ou plusieurs rôles RBAC soient attribués au principal de sécurité. Le service Stockage Azure fournit des rôles RBAC qui englobent des ensembles communs d’autorisations pour les données d’objet blob et de file d’attente. Les rôles qui sont attribués à un principal de sécurité déterminent les autorisations dont disposera le principal. Pour plus d’informations sur l’attribution de rôles RBAC pour le service Stockage Azure, consultez l’article [Manage access rights to storage data with RBAC](storage-auth-aad-rbac.md) (Gérer les droits d’accès aux données de stockage avec RBAC).

Les applications natives et les applications web qui adressent des requêtes aux services Azure BLOB ou de File d’attente peuvent également s’authentifier auprès d’Azure AD. Pour savoir comment demander un jeton d’accès et l’utiliser pour autoriser les requêtes adressées de données d’objet blob ou de file d’attente, consultez l’article [Authentification avec Azure AD à partir d’une application de stockage Azure](storage-auth-aad-app.md).

## <a name="assigning-rbac-roles-for-access-rights"></a>Attribution de rôles RBAC pour les droits d’accès

Azure Active Directory (Azure AD) autorise les droits d’accès aux ressources sécurisées via [RBAC (contrôle d’accès en fonction du rôle)](../../role-based-access-control/overview.md). Le service Stockage Azure définit un ensemble de rôles RBAC intégrés qui englobent les ensembles communs d’autorisations permettant d’accéder aux données d’objet blob ou de file d’attente. Vous pouvez également définir des rôles personnalisés pour l’accès aux données d’objet blob et de file d’attente.

Lorsqu’un rôle RBAC est attribué à un principal de sécurité Azure AD, Azure octroie l’accès à ces ressources pour ce principal de sécurité. L’accès peut être limité au niveau de l’abonnement, du groupe de ressources, du compte de stockage ou d’un conteneur ou d’une file d’attente individuelle. Un principal de sécurité Azure AD peut correspondre à un utilisateur, à un groupe, à un principal de service d’application ou à une [identité managée pour les ressources Azure](../../active-directory/managed-identities-azure-resources/overview.md).

### <a name="built-in-rbac-roles-for-blobs-and-queues"></a>Rôles RBAC intégrés pour les objets blob et les files d’attente

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

Pour plus d’informations sur l’attribution d’un rôle RBAC intégré à un principal de sécurité, consultez l’un des articles suivants :

- [Grant access to Azure blob and queue data with RBAC in the Azure portal](storage-auth-aad-rbac-portal.md) (Octroyer l’accès aux données d’objet blob et de file d’attente Azure avec RBAC dans le Portail Azure)
- [Octroyer l’accès aux données blob et de file d’attente Azure avec RBAC à l’aide d’Azure CLI](storage-auth-aad-rbac-cli.md)
- [Octroyer l’accès aux données blob et de file d’attente Azure avec RBAC à l’aide de PowerShell](storage-auth-aad-rbac-powershell.md)

Pour plus d’informations sur la définition des rôles intégrés pour le Stockage Azure, consultez [Comprendre les définitions de rôles](../../role-based-access-control/role-definitions.md#management-and-data-operations-preview). Pour plus d’informations sur la création de rôles RBAC personnalisés, consultez l’article [Créer des rôles personnalisés pour le contrôle d’accès en fonction du rôle Azure](../../role-based-access-control/custom-roles.md).

### <a name="access-permissions-for-data-operations"></a>Autorisations d’accès pour les opérations de données

Pour plus d’informations sur les autorisations requises pour l’appel d’opérations propres aux services Azure BLOB ou de File d’attente, consultez la section [Permissions for calling blob and queue data operations](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations) (Autorisations pour l’appel d’opérations de données d’objet blob et de file d’attente).

## <a name="resource-scope"></a>Étendue des ressources

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="access-data-with-an-azure-ad-account"></a>Accéder aux données avec un compte Azure AD

L’accès aux données d’objet blob ou de file d’attente par le biais du Portail Azure, de PowerShell ou d’Azure CLI peut être autorisé à l’aide du compte Azure AD de l’utilisateur ou au moyen des clés d’accès au compte (authentification par clé partagée).

### <a name="data-access-from-the-azure-portal"></a>Accès aux données à partir du Portail Azure

Le Portail Azure peut utiliser soit votre compte Azure AD, soit les clés d’accès au compte pour accéder aux données d’objet blob et de file d’attente dans un compte de stockage Azure. Le schéma d’autorisation utilisé par le Portail Azure dépend des rôles RBAC qui vous sont attribués.

Lorsque vous tentez d’accéder aux données d’objet blob ou de file d’attente, le Portail Azure commence par vérifier si vous avez reçu un rôle RBAC avec **Microsoft.Storage/storageAccounts/listkeys/action**. Si vous avez été doté d’un rôle avec cette action, le Portail Azure utilise la clé de compte pour l’accès aux données d’objet blob et de file d’attente par le biais de l’autorisation par clé partagée. Si vous n’avez pas reçu un rôle avec cette action, le Portail Azure tente d’accéder aux données à l’aide de votre compte Azure AD.

Pour accéder aux données d’objet blob ou de file d’attente à partir du Portail Azure à l’aide de votre compte Azure AD, vous devez disposer d’autorisations pour accéder à ces données, ainsi que pour parcourir les ressources de compte de stockage dans le Portail Azure. Les rôles intégrés fournis par le service Stockage Azure octroient l’accès aux ressources d’objet blob et de file d’attente, mais n’accordent pas d’autorisations pour les ressources de compte de stockage. C’est la raison pour laquelle l’accès au portail requiert également l’attribution d’un rôle Azure Resource Manager, tel que le rôle [Lecteur](../../role-based-access-control/built-in-roles.md#reader), étendu au niveau du compte de stockage ou à un niveau supérieur. Le rôle **Lecteur** octroie les autorisations les plus restreintes, mais l’utilisation d’un autre rôle Azure Resource Manager accordant l’accès aux ressources de gestion de compte de stockage est également acceptable. Pour plus d’informations sur l’attribution d’autorisations aux utilisateurs pour l’accès aux données dans le Portail Azure avec un compte Azure AD, consultez l’article [Grant access to Azure blob and queue data with RBAC in the Azure portal](storage-auth-aad-rbac-portal.md) (Octroyer l’accès aux données d’objet blob et de file d’attente Azure avec RBAC dans le Portail Azure).

Le Portail Azure indique le schéma d’autorisation en cours d’utilisation lorsque vous accédez à un conteneur ou à une file d’attente. Pour plus d’informations sur l’accès aux données dans le portail, consultez l’article [Use the Azure portal to access blob or queue data](storage-access-blobs-queues-portal.md) (Accéder aux données d’objet blob ou de file d’attente à l’aide du Portail Azure).

### <a name="data-access-from-powershell-or-azure-cli"></a>Accès aux données à partir de PowerShell ou d’Azure CLI

Azure CLI et PowerShell prennent en charge la connexion avec des informations d’identification Azure AD. Une fois que vous vous êtes connecté, votre session s’exécute sous ces informations d’identification. Pour plus d’informations, consultez l’article [Exécuter des commandes Azure CLI ou PowerShell avec des informations d’identification Azure AD pour accéder aux données d’objet blob ou de file d’attente](storage-auth-aad-script.md).

## <a name="azure-ad-authentication-over-smb-for-azure-files"></a>Authentification Azure AD sur SMB pour Azure Files

Azure Files prend en charge l’authentification avec Azure AD sur SMB uniquement pour les machines virtuelles jointes au domaine (préversion). Pour plus d’informations sur l’utilisation d’Azure AD sur SMB pour Azure Files, consultez l’article [Vue d’ensemble de l’authentification Azure Active Directory sur SMB pour Azure Files (préversion)](../files/storage-files-active-directory-overview.md).

## <a name="next-steps"></a>Étapes suivantes

- [Authenticate access to blobs and queues with Azure Active Directory and managed identities for Azure Resources](storage-auth-aad-msi.md) (Authentifier l’accès aux objets blob et files d’attente avec Azure Active Directory et les identités managées pour les ressources Azure)
- [S’authentifier auprès d’Azure Active Directory à partir d’une application pour accéder aux objets blob et aux files d’attente](storage-auth-aad-app.md)
- [Azure Storage support for Azure Active Directory based access control generally available](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/) (Disponibilité générale de la prise en charge par le service Stockage Azure du contrôle d’accès basé sur Azure Active Directory)
