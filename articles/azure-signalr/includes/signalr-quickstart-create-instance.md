---
title: Fichier Include
description: Fichier Include
author: anthonychu
ms.service: signalr
ms.topic: include
ms.date: 09/14/2018
ms.author: antchu
ms.custom: include file
ms.openlocfilehash: c95f05127d62d366e1ad78b45df23d04ced0164a
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2018
ms.locfileid: "53262779"
---
## <a name="create-an-azure-signalr-service-instance"></a>Créer une instance de service SignalR Azure

Votre application se connectera à l’instance de service SignalR dans Azure.

1. Cliquez sur le bouton Nouveau dans le coin supérieur gauche du portail Azure. À l’écran Nouveau, tapez *Service SignalR* dans la zone de recherche et appuyez sur Entrée.

    ![Rechercher Service SignalR](../media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-new.png)

1. Sélectionnez **Service SignalR** dans les résultats de la recherche, puis **Créer**.

1. Entrez les paramètres suivants.

    | Paramètre      | Valeur suggérée  | Description                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nom de la ressource** | Nom globalement unique | Le nom qui identifie votre nouvelle instance de service SignalR. Les caractères valides sont `a-z`, `0-9` et `-`.  | 
    | **Abonnement** | Votre abonnement | L’abonnement sous lequel cette nouvelle instance de service SignalR est créée. | 
    | **[Groupe de ressources](../../azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | Le nom du nouveau groupe de ressources dans lequel créer l’instance de service SignalR. | 
    | **Lieu** | USA Ouest | Sélectionnez une [région](https://azure.microsoft.com/regions/) proche de chez vous. |
    | **Niveau tarifaire** | Gratuit | Essayez gratuitement le service Azure SignalR. |
    | **Nombre d’unité** |  Non applicable | Le nombre d’unité spécifie le nombre de connexion que votre instance de service SignalR peut accepter. Ceci n’est configurable que dans le niveau Standard. |

    ![Créer un service SignalR](../media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-create.png)

1. Sélectionnez **Créer** pour commencer le déploiement de l’instance de service SignalR.