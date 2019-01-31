---
title: Modération de texte - Content Moderator
description: Utilisez la modération du texte pour détecter le texte indésirable, les informations d’identification personnelle (PII) et les listes de termes personnalisées.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 0b5cfdd69539caa7f9dcceb3e1d435d3a7e97fed
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55225463"
---
# <a name="learn-text-moderation-concepts"></a>Familiarisez-vous avec les concepts de modération de texte

Utilisez la modération du texte assistée par ordinateur et les fonctionnalités de [révision par des humains](Review-Tool-User-Guide/human-in-the-loop.md) de Content Moderator pour modérer le contenu du texte.

Vous bloquez, approuvez ou révisez le contenu en fonction de vos stratégies et de vos seuils. Utilisez-le pour augmenter la modération humaine dans des environnements où des partenaires, des employés et des consommateurs de génèrent du contenu de texte. Ces environnements incluent notamment les salles de conversation, les forums de discussion, les bots conversationnels, les catalogues d’e-commerce et les documents. 

La réponse du service inclut les informations suivantes :

- Vulgarité : correspondance basée sur des termes avec une liste prédéfinie de termes injurieux dans différentes langues
- Classification : classification en trois catégories assistée par ordinateur
- Informations d’identification personnelle (PII)
- Texte corrigé automatiquement
- Texte d’origine
- Langage

## <a name="profanity"></a>Termes vulgaires

Si l’API détecte des termes injurieux dans l’une des [langues prises en charge](Text-Moderation-API-Languages.md), ces termes sont inclus dans la réponse. La réponse contient également leur emplacement (`Index`) dans le texte d’origine. La valeur `ListId` dans l’exemple JSON suivant fait référence à des termes se trouvant dans les [listes de termes personnalisées](try-terms-list-api.md), le cas échéant.

    "Terms": [
    {
        "Index": 118,
        "OriginalIndex": 118,
        "ListId": 0,
        "Term": "crap"
    }

> [!NOTE]
> Pour le paramètre **langage**, attribuez `eng` ou laissez-le vide pour voir la réponse de la **classification** assistée par ordinateur (fonctionnalité d’évaluation). **Cette fonctionnalité prend en charge uniquement l’anglais**.
>
> Pour la détection des **termes injurieux**, utilisez le [code ISO 639-3](http://www-01.sil.org/iso639-3/codes.asp) des langues prises en charge répertoriées dans cet article, ou laissez le paramètre vide.

## <a name="classification"></a>classification ;

La **fonctionnalité de classification de texte** assistée par ordinateur de Content Moderator, qui prend en charge **uniquement l’anglais**, permet de détecter le contenu potentiellement indésirable. Le contenu marqué d’un indicateur peut être considéré comme inapproprié en fonction du contexte. La classification de texte informe de cette probabilité pour chaque catégorie et peut vous recommander une révision par des humains. Cette fonctionnalité utilise un modèle entraîné pour identifier un langage potentiellement abusif, dépréciatif ou discriminatoire. Cela inclut l’argot, les mots abrégés, les mots offensants et les mots intentionnellement mal orthographiés à réviser. 

L’extrait suivant de l’extrait de code JSON présente un exemple de sortie :

    "Classification": {
        "ReviewRecommended": true,
        "Category1": {
            "Score": 1.5113095059859916E-06
            },
        "Category2": {
            "Score": 0.12747249007225037
            },
        "Category3": {
            "Score": 0.98799997568130493
        }
    }

### <a name="explanation"></a>Explication

- `Category1` se réfère à la présence potentielle de langage pouvant être considéré comme sexuellement explicite ou réservé aux adultes dans certaines situations.
- `Category2` se réfère à la présence potentielle de langage pouvant être considéré comme sexuellement suggestif ou réservé aux adultes dans certaines situations.
- `Category3` se réfère à la présence potentielle de langage pouvant être considéré comme choquant dans certaines situations.
- `Score` est compris entre 0 et 1. Plus le score est élevé, plus le modèle prédit que la catégorie peut être applicable. Cette fonctionnalité s’appuie sur un modèle statistique plutôt que sur des résultats codés manuellement. Nous vous recommandons d’effectuer le test avec votre propre contenu afin de déterminer comment chaque catégorie correspond à vos besoins.
- La valeur de `ReviewRecommended` est true ou false, selon les seuils de score internes. Les clients doivent évaluer l’opportunité d’utiliser cette valeur ou choisir des seuils personnalisés en fonction de leurs stratégies de contenu.

## <a name="personally-identifiable-information-pii"></a>Informations d’identification personnelle (PII)

La fonctionnalité PII détecte la présence potentielle des informations suivantes :

- Adresse de messagerie
- Adresse postale aux États-Unis
- Adresse IP
- Numéro de téléphone aux États-Unis
- Numéro de téléphone au Royaume-Uni
- Numéro de sécurité sociale (SSN)

L’exemple suivant illustre une réponse :

    "PII": {
        "Email": [{
            "Detected": "abcdef@abcd.com",
            "SubType": "Regular",
            "Text": "abcdef@abcd.com",
            "Index": 32
            }],
        "IPA": [{
            "SubType": "IPV4",
            "Text": "255.255.255.255",
            "Index": 72
            }],
        "Phone": [{
            "CountryCode": "US",
            "Text": "6657789887",
            "Index": 56
            }, {
            "CountryCode": "US",
            "Text": "870 608 4000",
            "Index": 212
            }, {
            "CountryCode": "UK",
            "Text": "+44 870 608 4000",
            "Index": 208
            }, {
            "CountryCode": "UK",
            "Text": "0344 800 2400",
            "Index": 228
            }, {
            "CountryCode": "UK",
            "Text": "0800 820 3300",
            "Index": 245
            }],
        "Address": [{
            "Text": "1 Microsoft Way, Redmond, WA 98052",
            "Index": 89
            }],
        "SSN": [{
            "Text": "999999999",
            "Index": 56
            }, {
            "Text": "999-99-9999",
            "Index": 267
            }]
        }

## <a name="auto-correction"></a>Correction automatique

Supposons que le texte d’entrée soit le suivant (les fautes « lzay » et « f0x » sont intentionnelles) :

    The qu!ck brown f0x jumps over the lzay dog.

Si vous demandez la correction automatique, la réponse contient la version corrigée du texte :

    The quick brown fox jumps over the lazy dog.

## <a name="creating-and-managing-your-custom-lists-of-terms"></a>Création et gestion de vos listes personnalisées de termes

Bien que la liste globale par défaut de termes fonctionne parfaitement pour la plupart des cas, vous pouvez passer au crible un texte pour détecter des termes qui sont spécifiques à vos besoins professionnels. Vous pouvez, par exemple, filtrer tous les noms de marques de concurrents dans des posts d’utilisateurs.

> [!NOTE]
> Il existe une limite maximale de **5 listes de termes**, chaque liste ne devant **pas dépasser 10 000 termes**.
>

L’exemple suivant présente l’ID de liste correspondant :

    "Terms": [
    {
        "Index": 118,
        "OriginalIndex": 118,
        "ListId": 231.
        "Term": "crap"
    }

Content Moderator fournit une [API de liste de termes](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f) avec des opérations pour la gestion des listes de termes personnalisées. Commencez avec la [Console d’API de listes de termes personnalisées](try-terms-list-api.md) et utilisez les exemples de code API REST. Consultez également le [démarrage rapide .NET des listes de termes](term-lists-quickstart-dotnet.md) si vous connaissez déjà Visual Studio et C#.

## <a name="next-steps"></a>Étapes suivantes

Évaluez la [Console d’API Modération du texte](try-text-api.md) et utilisez les exemples de code API REST. Consultez également le [démarrage rapide .NET de l’API de modération du texte](text-moderation-quickstart-dotnet.md) si vous connaissez déjà Visual Studio et C#.
