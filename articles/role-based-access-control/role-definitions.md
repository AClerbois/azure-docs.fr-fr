---
title: Comprendre les définitions de rôles dans des ressources RBAC pour Azure | Microsoft Docs
description: Apprenez-en davantage sur les définitions de rôles dans le cadre du contrôle d’accès en fonction du rôle (RBAC) pour une gestion affinée des accès aux ressources Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/09/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: 7855c2bd45ba35ecb0ede5c60268e6446f37ed5a
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "58804528"
---
# <a name="understand-role-definitions-for-azure-resources"></a>Comprendre les définitions de rôle relatives aux ressources Azure

Si vous essayez de comprendre comment un rôle fonctionne, ou si vous créez votre propre [rôle personnalisé pour des ressources Azure](custom-roles.md), il est utile de comprendre la façon dont les rôles sont définis. Cet article décrit en détail les définitions de rôles et fournit quelques exemples.

## <a name="role-definition-structure"></a>Structure d’une définition de rôle

Une *définition de rôle* est une collection d’autorisations. On l’appelle parfois simplement *rôle*. Une définition de rôle répertorie les opérations qui peuvent être effectuées, telles que lire, écrire et supprimer. Elle permet également de répertorier les opérations qui ne peuvent pas être effectuées ou les opérations liées aux données sous-jacentes. Une définition de rôle présente la structure suivante :

```
Name
Id
IsCustom
Description
Actions []
NotActions []
DataActions []
NotDataActions []
AssignableScopes []
```

Les opérations sont spécifiées à l’aide de chaînes dont le format est le suivant :

- `{Company}.{ProviderName}/{resourceType}/{action}`

La portion `{action}` d’une chaîne d’opération spécifie le type des opérations que vous pouvez effectuer sur un type de ressource. Par exemple, vous verrez les sous-chaînes suivantes dans `{action}` :

| Sous-chaîne d’action    | Description         |
| ------------------- | ------------------- |
| `*` | Le caractère générique donne accès à toutes les opérations qui correspondent à la chaîne. |
| `read` | Permet les opérations de lecture (GET). |
| `write` | Permet les opérations d’écriture (PUT, POST et PATCH). |
| `delete` | Permet les opérations de suppression (DELETE). |

Voici la définition du rôle [Contributeur](built-in-roles.md#contributor) au format JSON. L’opération de caractère générique (`*`) sous `Actions` indique que le principal affecté à ce rôle peut effectuer toutes les actions, ou, en d’autres termes, tout gérer. Cela inclut les actions qui seront définies dans le futur, à mesure qu’Azure ajoutera de nouveaux types de ressources. Les opérations sous `NotActions` sont soustraites de `Actions`. Dans le cas du rôle [Contributeur](built-in-roles.md#contributor), `NotActions` supprime la possibilité pour ce rôle de gérer et d’autoriser l’accès aux ressources.

```json
{
  "Name": "Contributor",
  "Id": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "IsCustom": false,
  "Description": "Lets you manage everything except access to resources.",
  "Actions": [
    "*"
  ],
  "NotActions": [
    "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action"
  ],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

## <a name="management-and-data-operations-preview"></a>Opérations de gestion et sur les données (préversion)

Le contrôle d’accès en fonction du rôle pour les opérations de gestion est spécifié dans les propriétés `Actions` et `NotActions` d’une définition de rôle. Voici quelques exemples d’opérations de gestion dans Azure :

- Gérer l’accès à un compte de stockage
- Créer, mettre à jour ou supprimer un conteneur d’objets blob
- Supprimer un groupe de ressources et toutes ses ressources

Vos données n’héritent pas de l’accès à la gestion. Cette séparation empêche des rôles avec caractères génériques (`*`) d’avoir un accès illimité à vos données. Par exemple, si un utilisateur a un rôle [Lecteur](built-in-roles.md#reader) sur un abonnement, il peut afficher le compte de stockage, mais pas les données sous-jacentes, par défaut.

Auparavant, le contrôle d’accès en fonction du rôle n’était pas utilisé pour les opérations sur les données. L’autorisation pour les opérations sur les données variait selon les fournisseurs de ressources. Le même modèle d’autorisation du contrôle d'accès en fonction du rôle utilisé pour les opérations de gestion a été étendu aux opérations sur les données (actuellement en préversion).

Pour prendre en charge les opérations sur les données, de nouvelles propriétés de données ont été ajoutées à la structure de définition de rôle. Les opérations sur les données sont spécifiées dans les propriétés `DataActions` et `NotDataActions`. En ajoutant ces propriétés de données, la séparation entre la gestion et les données est conservée. Cela empêche les attributions de rôle contenant des caractères génériques (`*`) d’accéder soudainement aux données. Voici quelques opérations sur les données qui peuvent être spécifiées dans `DataActions` et `NotDataActions` :

- Lire une liste d’objets blob dans un conteneur
- Écrire un objet blob de stockage dans un conteneur
- Supprimer un message dans une file d’attente

Voici le [lecteur de données de stockage Blob](built-in-roles.md#storage-blob-data-reader) définition de rôle, qui inclut des opérations à la fois dans le `Actions` et `DataActions` propriétés. Ce rôle vous permet de lire le conteneur d’objets blob ainsi que les données d’objets blob sous-jacentes.

```json
{
  "Name": "Storage Blob Data Reader",
  "Id": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "IsCustom": false,
  "Description": "Allows for read access to Azure Storage blob containers and data",
  "Actions": [
    "Microsoft.Storage/storageAccounts/blobServices/containers/read"
  ],
  "NotActions": [],
  "DataActions": [
    "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read"
  ],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

Seules des opérations sur les données peuvent être ajoutées aux propriétés `DataActions` et `NotDataActions`. Les fournisseurs de ressources identifient quelles opérations sont des opérations sur les données, en définissant la propriété `isDataAction` sur `true`. Pour afficher la liste des opérations où `isDataAction` est `true`, consultez [Opérations de fournisseur de ressources](resource-provider-operations.md). Les rôles qui n’ont pas d’opérations sur les données ne sont pas obligés d’avoir les propriétés `DataActions` et `NotDataActions` dans la définition de rôle.

L’autorisation pour tous les appels d’API des opérations de gestion est gérée par Azure Resource Manager. L’autorisation pour les appels d’API des opérations sur les données est gérée par un fournisseur de ressources ou Azure Resource Manager.

### <a name="data-operations-example"></a>Exemple d’opérations sur les données

Pour mieux comprendre comment fonctionnent les opérations de gestion et les opérations sur les données, prenons un exemple spécifique. Alice a reçu le rôle [Propriétaire](built-in-roles.md#owner) au niveau de l’étendue de l’abonnement. Bob a été attribué le [contributeur aux données stockage Blob](built-in-roles.md#storage-blob-data-contributor) rôle dans une étendue de compte de stockage. Le diagramme qui suit présente cet exemple.

![Le contrôle d’accès en fonction du rôle a été étendu pour prendre en charge les opérations de gestion et les opérations sur les données](./media/role-definitions/rbac-management-data.png)

Le [propriétaire](built-in-roles.md#owner) rôle pour Alice et [contributeur aux données stockage Blob](built-in-roles.md#storage-blob-data-contributor) rôle pour Bob a les actions suivantes :

Propriétaire

&nbsp;&nbsp;&nbsp;&nbsp;Actions<br>
&nbsp;&nbsp;&nbsp;&nbsp;`*`

Contributeur aux données Blob du stockage

&nbsp;&nbsp;&nbsp;&nbsp;Actions<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/write`<br>
&nbsp;&nbsp;&nbsp;&nbsp;DataActions<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write`

Comme Alice dispose d’une action avec caractère générique (`*`) à une étendue de l’abonnement, elle hérite d’autorisations lui permettant d’effectuer toutes les actions de gestion. Alice peut lire, écrire et supprimer des conteneurs. En revanche, elle ne peut pas effectuer d’opérations sur des données sans passer par des étapes supplémentaires. Par exemple, par défaut, Alice ne peut pas lire les objets blob à l’intérieur d’un conteneur. Pour cela, elle doit récupérer les clés d’accès de stockage et les utiliser pour accéder aux objets blob.

Les autorisations de Bob sont limitées à simplement le `Actions` et `DataActions` spécifié dans le [contributeur aux données stockage Blob](built-in-roles.md#storage-blob-data-contributor) rôle. En fonction du rôle, Bob peut effectuer à la fois des opérations de gestion et des opérations sur les données. Par exemple, Bob peut lire, écrire et supprimer des conteneurs du compte de stockage spécifié, mais aussi lire, écrire et supprimer les objets blob.

Pour plus d’informations sur la gestion et la sécurité du plan de données pour le stockage, consultez le [guide de sécurité Stockage Microsoft Azure](../storage/common/storage-security-guide.md).

### <a name="what-tools-support-using-rbac-for-data-operations"></a>Quels outils prennent en charge l’utilisation de RBAC pour les opérations sur les données ?

Pour afficher et utiliser des opérations sur les données, vous devez disposer des versions appropriées des outils ou des kits de développement logiciel (SDK) :

| Outil  | Version  |
|---------|---------|
| [Azure PowerShell](/powershell/azure/install-az-ps) | 1.1.0 ou ultérieure |
| [Interface de ligne de commande Azure](/cli/azure/install-azure-cli) | 2.0.30 ou version ultérieure |
| [Azure pour .NET](/dotnet/azure/) | 2.8.0-preview ou version ultérieure |
| [Kit de développement logiciel (SDK) Azure pour Go](/go/azure/azure-sdk-go-install) | 15.0.0 ou version ultérieure |
| [Azure pour Java](/java/azure/) | 1.9.0 ou version ultérieure |
| [Azure pour Python](/python/azure) | 0.40.0 ou version ultérieure |
| [Kit de développement logiciel (SDK) Azure pour Ruby](https://rubygems.org/gems/azure_sdk) | 0.17.1 ou version ultérieure |

Pour afficher et utiliser les opérations de données dans l’API REST, vous devez définir le paramètre **api-version** sur la version suivante ou ultérieure :

- 2018-01-01-preview

Le portail Azure permet également aux utilisateurs de parcourir et de gérer le contenu des files d’attente et conteneurs d'objets Blob via l'expérience de préversion Azure AD. Pour afficher et gérer le contenu d'une file d'attente ou d'un conteneur d'objets Blob, cliquez sur **Explorer les données à l'aide de la préversion Azure AD** sur la vue d’ensemble du compte de stockage.

![Explorer les files d'attente et les conteneurs d'objets Blob à l'aide de la préversion Azure AD](./media/role-definitions/rbac-dataactions-browsing.png)

## <a name="actions"></a>Actions

L’autorisation `Actions` spécifie les opérations d’administration que le rôle autorise. Il s’agit d’un ensemble de chaînes d’opération qui identifient les opérations sécurisables des fournisseurs de ressources Azure. Voici quelques exemples d’opérations de gestion qui peuvent être utilisées dans `Actions`.

| Chaîne d’opération    | Description         |
| ------------------- | ------------------- |
| `*/read` | Accorde l’accès aux opérations de lecture pour tous les types de ressources de l’ensemble des fournisseurs de ressources Azure.|
| `Microsoft.Compute/*` | Accorde l’accès à l’ensemble des opérations pour tous les types de ressources dans le fournisseur de ressources Microsoft.Compute.|
| `Microsoft.Network/*/read` | Accorde l’accès aux opérations de lecture pour tous les types de ressources dans le fournisseur de ressources Microsoft.Network d’Azure.|
| `Microsoft.Compute/virtualMachines/*` | Accorde l’accès à toutes les opérations des machines virtuelles et à leurs types de ressources enfants.|
| `microsoft.web/sites/restart/Action` | Accorde l’accès au redémarrage d’une application web.|

## <a name="notactions"></a>NotActions

L’autorisation `NotActions` spécifie les opérations de gestion qui sont exclues des `Actions` autorisées. Utilisez l’autorisation `NotActions` si l’ensemble des opérations que vous souhaitez autoriser est plus facile à définir en excluant les opérations restreintes. L’accès accordé par un rôle (autorisations effectives) est calculé en soustrayant les opérations `NotActions` des opérations `Actions`.

> [!NOTE]
> Si un utilisateur se voit attribuer un rôle qui exclut une opération dans `NotActions`, et un second rôle qui accorde l’accès à cette même opération, il est autorisé à effectuer celle-ci. `NotActions` n’est pas une règle de refus : il s’agit simplement d’un moyen pratique pour créer un ensemble d’opérations autorisées lorsque des opérations spécifiques doivent être exclues.
>

## <a name="dataactions-preview"></a>DataActions (préversion)

L’autorisation `DataActions` spécifie les opérations de données que le rôle autorise sur vos données au sein de cet objet. Par exemple, si un utilisateur dispose d’un accès en lecture aux données blob d’un compte de stockage, il peut lire les objets blob de ce compte de stockage. Voici quelques exemples d’opérations sur les données qui peuvent être utilisées dans `DataActions`.

| Chaîne d’opération    | Description         |
| ------------------- | ------------------- |
| `Microsoft.Storage/storageAccounts/ blobServices/containers/blobs/read` | Retourne un objet blob ou une liste d'objets blob. |
| `Microsoft.Storage/storageAccounts/ blobServices/containers/blobs/write` | Retourne le résultat de l'écriture d'un objet blob. |
| `Microsoft.Storage/storageAccounts/ queueServices/queues/messages/read` | Retourne un message. |
| `Microsoft.Storage/storageAccounts/ queueServices/queues/messages/*` | Retourne un message ou le résultat de l’écriture ou de la suppression d’un message. |

## <a name="notdataactions-preview"></a>NotDataActions (préversion)

L’autorisation `NotDataActions` spécifie les opérations sur les données qui sont exclues des `DataActions` autorisées. L’accès accordé par un rôle (autorisations effectives) est calculé en soustrayant les opérations `NotDataActions` des opérations `DataActions`. Chaque fournisseur de ressources fournit son propre ensemble d’API pour répondre à des opérations sur les données.

> [!NOTE]
> Si un utilisateur se voit attribuer un rôle qui exclut une opération sur les données dans `NotDataActions`, et un second rôle qui accorde l’accès à cette même opération, il est autorisé à effectuer celle-ci. `NotDataActions` n’est pas une règle de refus : il s’agit simplement d’un moyen pratique pour créer un ensemble d’opérations sur les données autorisées lorsque des opérations sur les données spécifiques doivent être exclues.
>

## <a name="assignablescopes"></a>AssignableScopes

La propriété `AssignableScopes` spécifie les étendues (abonnements, groupes de ressources ou ressources) dans lesquelles le rôle est disponible pour attribution. Vous pouvez rendre le rôle disponible pour attribution uniquement dans les abonnements ou les groupes de ressources qui le nécessitent, mais pas surcharger l’expérience utilisateur pour le reste des abonnements ou groupes de ressources. Vous devez utiliser au moins un abonnement, groupe de ressources ou ID de ressource.

La chaîne `AssignableScopes` est définie sur l’étendue racine (`"/"`) pour les rôles intégrés. L’étendue racine indique que le rôle est disponible pour attribution dans toutes les étendues. Voici des exemples d’étendues assignables valides :

| Scénario | Exemples |
|----------|---------|
| Rôle disponible pour attribution dans un seul abonnement | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"` |
| Rôle disponible pour attribution dans deux abonnements | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e", "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"` |
| Rôle disponible pour attribution uniquement dans le groupe de ressources réseau | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network"` |
| Rôle disponible pour attribution dans toutes les étendues | `"/"` |

Pour plus d’informations sur `AssignableScopes` pour des rôles personnalisés, consultez [Rôles personnalisés pour les ressources Azure](custom-roles.md).

## <a name="next-steps"></a>Étapes suivantes

* [Rôles intégrés pour les ressources Azure](built-in-roles.md)
* [Rôles personnalisés pour les ressources Azure](custom-roles.md)
* [Opérations du fournisseur de ressources Azure Resource Manager](resource-provider-operations.md)
