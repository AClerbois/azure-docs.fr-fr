---
title: 'Exemple : Ajouter des visages à un objet PersonGroup - API Visage'
titleSuffix: Azure Cognitive Services
description: Utilisez l’API Visage pour ajouter des visages à une image.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 04/10/2019
ms.author: sbowles
ms.openlocfilehash: 83aef90702e4a4cc4fd9bdfda486841f9b2a63a4
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66124499"
---
# <a name="add-faces-to-a-persongroup"></a>Ajouter des visages à un objet PersonGroup

Ce guide explique comment ajouter un grand nombre de personnes et de visages à un objet PersonGroup. Cette stratégie s'applique également aux objets LargePersonGroup, FaceList et LargeFaceList. Cet exemple est écrit en C# à l'aide de la bibliothèque de client .NET de l'API Visage d'Azure Cognitive Services.

## <a name="step-1-initialization"></a>Étape 1 : Initialisation

Le code suivant déclare plusieurs variables et implémente une fonction d'assistance pour planifier les requêtes d'ajout de visage :

- `PersonCount` correspond au nombre total de personnes.
- `CallLimitPerSecond` correspond au nombre maximal d’appels par seconde en fonction du niveau d’abonnement.
- `_timeStampQueue` est une file d'attente qui permet d’enregistrer les horodatages des requêtes.
- `await WaitCallLimitPerSecondAsync()` attend qu'elle soit valide pour envoyer la requête suivante.

```csharp
const int PersonCount = 10000;
const int CallLimitPerSecond = 10;
static Queue<DateTime> _timeStampQueue = new Queue<DateTime>(CallLimitPerSecond);

static async Task WaitCallLimitPerSecondAsync()
{
    Monitor.Enter(_timeStampQueue);
    try
    {
        if (_timeStampQueue.Count >= CallLimitPerSecond)
        {
            TimeSpan timeInterval = DateTime.UtcNow - _timeStampQueue.Peek();
            if (timeInterval < TimeSpan.FromSeconds(1))
            {
                await Task.Delay(TimeSpan.FromSeconds(1) - timeInterval);
            }
            _timeStampQueue.Dequeue();
        }
        _timeStampQueue.Enqueue(DateTime.UtcNow);
    }
    finally
    {
        Monitor.Exit(_timeStampQueue);
    }
}
```

## <a name="step-2-authorize-the-api-call"></a>Étape 2 : Autoriser l’appel d’API

Lorsque vous utilisez une bibliothèque de client, vous devez transmettre votre clé d'abonnement au constructeur de la classe FaceServiceClient. Par exemple : 

```csharp
FaceServiceClient faceServiceClient = new FaceServiceClient("<Subscription Key>");
```

Pour obtenir la clé d'abonnement, accédez à la Place de marché Azure à partir du portail Azure. Pour plus d'informations, consultez [Abonnements](https://www.microsoft.com/cognitive-services/sign-up).

## <a name="step-3-create-the-persongroup"></a>Étape 3 : Créer PersonGroup

Un groupe PersonGroup nommé « MyPersonGroup » est créé pour enregistrer les personnes.
L’heure de la requête est empilée dans `_timeStampQueue` pour garantir la validation globale.

```csharp
const string personGroupId = "mypersongroupid";
const string personGroupName = "MyPersonGroup";
_timeStampQueue.Enqueue(DateTime.UtcNow);
await faceServiceClient.CreatePersonGroupAsync(personGroupId, personGroupName);
```

## <a name="step-4-create-the-persons-for-the-persongroup"></a>Étape 4 : Créer les personnes pour l'objet PersonGroup

Les personnes sont créées simultanément, et `await WaitCallLimitPerSecondAsync()` est également appliqué pour éviter de dépasser la limite d'appel.

```csharp
CreatePersonResult[] persons = new CreatePersonResult[PersonCount];
Parallel.For(0, PersonCount, async i =>
{
    await WaitCallLimitPerSecondAsync();

    string personName = $"PersonName#{i}";
    persons[i] = await faceServiceClient.CreatePersonAsync(personGroupId, personName);
});
```

## <a name="step-5-add-faces-to-the-persons"></a>Étape 5 : Ajouter des visages aux personnes

Les visages ajoutés à différentes personnes sont traités simultanément. Les visages ajoutés à une personne spécifique sont traités de manière séquentielle.
Là encore, `await WaitCallLimitPerSecondAsync()` est appelé afin de garantir que la fréquence de requêtes se situe dans le cadre de la limitation.

```csharp
Parallel.For(0, PersonCount, async i =>
{
    Guid personId = persons[i].PersonId;
    string personImageDir = @"/path/to/person/i/images";

    foreach (string imagePath in Directory.GetFiles(personImageDir, "*.jpg"))
    {
        await WaitCallLimitPerSecondAsync();

        using (Stream stream = File.OpenRead(imagePath))
        {
            await faceServiceClient.AddPersonFaceAsync(personGroupId, personId, stream);
        }
    }
});
```

## <a name="summary"></a>Résumé

Dans ce guide, vous avez appris à créer un objet PersonGroup avec un nombre important de personnes et de visages. Plusieurs rappels :

- Cette stratégie s'applique également aux objets FaceList et LargePersonGroup.
- L'ajout et la suppression de visages dans différents objets FaceList ou personnes de l'objet LargePersonGroup sont traités simultanément.
- L'ajout et la suppression de visages dans un objet FaceList donné ou une personne spécifique d'un objet LargePersonGroup s'effectuent de façon séquentielle.
- Par souci de simplicité, le traitement des exceptions éventuelles n'est pas abordé dans ce guide. Si vous souhaitez améliorer la robustesse, appliquez la stratégie de nouvelles tentatives appropriée.

Les fonctionnalités suivantes ont été décrites et illustrées :

- Création d'objets PersonGroups à l'aide de l'API [PersonGroup - Créer](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244)
- Création de personnes à l'aide de l'API [PersonGroup Person - Créer](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)
- Ajout de visages aux personnes à l'aide de l'API [PersonGroup Person - Ajouter un visage](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)

## <a name="related-topics"></a>Rubriques connexes

- [Identifier des visages sur une image](HowtoIdentifyFacesinImage.md)
- [Détecter des visages sur une image](HowtoDetectFacesinImage.md)
- [Utiliser la fonctionnalité à grande échelle](how-to-use-large-scale.md)
