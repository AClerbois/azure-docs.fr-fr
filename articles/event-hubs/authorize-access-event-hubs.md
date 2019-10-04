---
title: Autoriser l’accès à Azure Event Hubs
description: Cet article fournit des informations sur les différentes options qui permettent d'autoriser l'accès aux ressources Azure Event Hubs.
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: f01758c70e52f96fcd22a94e9b83f910cbf200c9
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/26/2019
ms.locfileid: "70035867"
---
# <a name="authorize-access-to-azure-event-hubs"></a>Autoriser l’accès à Azure Event Hubs
Chaque fois que vous publiez ou utilisez des événements/données à partir d'un hub d'événements, votre client tente d'accéder aux ressources Event Hubs. Chaque demande adressée à une ressource sécurisée doit être autorisée afin que le service puisse vérifier que le client dispose des autorisations requises pour publier/utiliser les données. 

Pour autoriser l'accès aux ressources sécurisées, Azure Event Hubs propose les options suivantes :

## <a name="azure-active-directory"></a>Azure Active Directory
L'intégration Azure Active Directory (Azure AD) pour les ressources Event Hubs fournit un contrôle d'accès en fonction du rôle (RBAC) pour un contrôle affiné de l'accès d'un client aux ressources. Vous pouvez utiliser le contrôle d'accès en fonction du rôle (RBAC) pour accorder des autorisations à un principal de sécurité, qui peut être un utilisateur, un groupe ou un principal de service d'application. Le principal de sécurité est authentifié par Azure AD pour retourner un jeton OAuth 2.0. Le jeton peut être utilisé pour autoriser une demande d'accès à une ressource Event Hubs.

Pour plus d'informations sur l'authentification à l'aide d'Azure AD, consultez les articles suivants :

- [Authentifier les requêtes adressées à Azure Event Hubs à l'aide d'Azure Active Directory](authenticate-application.md)
- [Autoriser l'accès aux ressources Event Hubs à l'aide d'Azure Active Directory](authorize-access-azure-active-directory.md)

## <a name="share-access-signatures"></a>Signatures d'accès partagé 
Les signatures d'accès partagé (SAP) des ressources Event Hubs fournissent un accès délégué limité aux ressources Event Hubs. L'ajout de contraintes sur l'intervalle de temps pour lequel la signature est valide ou sur les autorisations qu'elle accorde offre une certaine souplesse en matière de gestion des ressources. Pour plus d'informations, consultez [S'authentifier à l'aide de signatures d'accès partagé (SAP)](authenticate-shared-access-signature.md). 

Le fait d'autoriser des utilisateurs ou des applications à l'aide d'un jeton OAuth 2.0 renvoyé par Azure AD confère une sécurité et une facilité d'utilisation supérieures à ce qu'offrent les signatures d'accès partagé. Avec Azure AD, il n'est pas nécessaire de stocker les jetons d'accès avec votre code et de risquer d'éventuelles failles de sécurité. Vous pouvez continuer à utiliser des signatures d'accès partagé pour accorder un accès affiné aux ressources Event Hubs. Mais sachez qu'Azure AD offre des fonctionnalités similaires qui ne nécessitent aucune gestion des jetons SAS ni aucune révocation des signatures d'accès partagé compromises. 

Par défaut, toutes les ressources Event Hubs sont sécurisées, et le propriétaire du compte est le seul à y avoir accès. Cela dit, vous pouvez utiliser n'importe laquelle des stratégies d'autorisation décrites ci-dessus pour permettre aux clients d'accéder aux ressources Event Hubs. Seules les ressources Event Hubs créées avec le modèle de déploiement Azure Resource Manager prennent en charge l'autorisation Azure AD. Chaque fois que possible, Microsoft recommande d'utiliser Azure AD pour un maximum de sécurité et de facilité d'utilisation.

Pour plus d'informations sur l'autorisation à l'aide de SAS, consultez [Autoriser l'accès aux ressources Event Hubs à l'aide de signatures d'accès partagé](authorize-access-shared-access-signature.md).

## <a name="next-steps"></a>Étapes suivantes
- Consultez les [Exemples de RBAC](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac) publiés dans notre référentiel GitHub. 
- Consultez les articles suivants :
    - [Authentifier les requêtes adressées à Azure Event Hubs à partir d'une application à l'aide d'Azure Active Directory](authenticate-application.md)
    - [Authentifier une identité managée avec Azure Active Directory pour accéder aux ressources Event Hubs](authenticate-managed-identity.md)
    - [Authentifier les demandes adressées à Azure Event Hubs à l'aide de signatures d'accès partagé](authenticate-shared-access-signature.md)
    - [Autoriser l'accès aux ressources Event Hubs avec Azure Active Directory](authorize-access-azure-active-directory.md)
    - [Autoriser l'accès aux ressources Event Hubs avec des signatures d'accès partagé](authorize-access-shared-access-signature.md)

