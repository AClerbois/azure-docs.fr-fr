---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: wesmc
ms.openlocfilehash: 8ebf5ddfa118e0aeadeab0c00a981871a4b5708e
ms.sourcegitcommit: b254db346732b64678419db428fd9eb200f3c3c5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2018
ms.locfileid: "53429961"
---
Les applications .NET peuvent utiliser le client de cache **StackExchange.Redis** , qui peut être configuré dans Visual Studio à l’aide d’un package NuGet simplifiant la configuration des applications clientes de cache. 

> [!NOTE]
> Pour plus d’informations, consultez la page GitHub [StackExchange.Redis](http://github.com/StackExchange/StackExchange.Redis) et la [documentation du client de Cache StackExchange.Azure pour Redis](http://github.com/StackExchange/StackExchange.Redis#documentation).
>
>

Pour configurer une application cliente dans Visual Studio avec le package NuGet StackExchange.Redis, cliquez avec le bouton droit sur le projet dans l’**Explorateur de solutions** et choisissez **Gérer les packages NuGet**. 

![Manage NuGet packages](media/redis-cache-configure-stackexchange-redis-nuget/redis-cache-manage-nuget-menu.png)

Tapez **StackExchange.Redis** ou **StackExchange.Redis.StrongName** dans la zone de texte de recherche, sélectionnez la version souhaitée dans la liste des résultats, puis cliquez sur **Installer**.

> [!NOTE]
> Si vous préférez utiliser une version avec nom fort de la bibliothèque du client **StackExchange.Redis**, choisissez **StackExchange.Redis.StrongName**. Sinon, choisissez **StackExchange.Redis**.
>
>

![StackExchange.Redis NuGet package](media/redis-cache-configure-stackexchange-redis-nuget/redis-cache-stackexchange-redis.png)

Le package NuGet télécharge et ajoute les références d’assembly nécessaires pour que votre application cliente puisse accéder au cache Azure pour Redis avec le client du cache StackExchange.Azure pour Redis.

> [!NOTE]
> Si vous avez déjà configuré votre projet pour utiliser StackExchange.Redis, vous pouvez vérifier les mises à jour du package à partir du **gestionnaire de package NuGet**. Pour rechercher et installer les versions mises à jour du package NuGet StackExchange.Redis, cliquez sur **Mises à jour** dans la fenêtre du **gestionnaire de package NuGet**. Si une mise à jour du package StackExchange.Redis NuGet est disponible, vous pouvez mettre à jour votre projet afin d’utiliser cette version mise à jour.
>
>

Vous pouvez également installer le package NuGet StackExchange.Redis en cliquant sur **Gestionnaire de package NuGet**, **Console du Gestionnaire de package** dans le menu **Outils**, et en exécutant la commande suivante à partir de la fenêtre **Console du gestionnaire de package**.

```
Install-Package StackExchange.Redis
```
