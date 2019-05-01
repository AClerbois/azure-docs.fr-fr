---
title: Migrer vers l’API de traduction de texte Translator Text v3
titlesuffix: Azure Cognitive Services
description: Découvrez comment migrer l’API de traduction de texte Translator Text de la V2 à la V3.
services: cognitive-services
author: v-pawal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: v-jansko
ms.openlocfilehash: fa1733ddd70de3f8ef93452a880094ccf8b9ef56
ms.sourcegitcommit: 807c318f5c034f8256f91c241e9d6f8f4d7de90a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64951473"
---
# <a name="translator-text-api-v2-to-v3-migration"></a>Migration de l’API de traduction de texte Translator Text de la v2 à la v3

> [!NOTE]
> V2 a été déconseillée à partir du 30 avril 2018. Veuillez migrer vos applications vers V3 pour tirer parti des nouvelles fonctionnalités disponibles exclusivement dans V3.
> 
> Microsoft Translator Hub seront supprimé le 17 mai 2019. [Afficher des dates et des informations sur la migration important](https://www.microsoft.com/translator/business/hub/).  

L’équipe Microsoft Translator a publié la version 3 (v3) de l’API de traduction de texte Translator Text. Cette version inclut de nouvelles fonctionnalités, les méthodes déconseillées et un nouveau format pour l’envoi et la réception de données à partir du service de traduction de Microsoft. Ce document fournit des informations sur la modification des applications pour utiliser la V3. 

La fin de ce document contient des liens utiles pour vous permettre d’en savoir plus.

## <a name="summary-of-features"></a>Résumé des fonctionnalités

* Sans trace - L’approche Sans trace s’applique à tous les niveaux tarifaires du portail Azure dans la V3. Cette fonctionnalité signifie qu’aucun texte soumis à l’API v3 ne sera enregistré par Microsoft.
* JSON - XML est remplacé par JSON. Toutes les données envoyées au service et reçues à partir du service sont au format JSON.
* Plusieurs langues cible dans une requête unique - La méthode Translate accepte plusieurs langues cible pour la traduction dans une requête unique. Par exemple, une requête unique peut être de l’anglais vers l’allemand, l’espagnol et le japonais ou tout autre groupe de langues.
* Bilingual dictionary - Une méthode Bilingual dictionary a été ajoutée à l’API. Cette méthode inclut la « recherche » et les « exemples ».
* Transliterate - Une méthode Transliterate a été ajoutée à l’API. Cette méthode convertit les mots ou les phrases d’un script (par exemple : arabe) dans un autre script (par exemple : latin).
* Languages - Une nouvelle méthode « Languages » donne des informations linguistiques au format JSON, à utiliser avec les méthodes Translate, Dictionary et Transliterate.
* Nouveautés pour la méthode Translate - De nouvelles fonctionnalités ont été ajoutées à la méthode « Translate » pour prendre en charge certaines des fonctionnalités qui étaient auparavant des méthodes distinctes dans l’API V2. Par exemple, TranslateArray.
* Méthode Speak - La fonctionnalité de synthèse vocale n’est plus prise en charge dans l’API Microsoft Translator. Elle est disponible dans le [service Microsoft Speech](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech).

La liste suivante des méthodes V2 et V3 identifie les méthodes V3 et les API qui fourniront les fonctionnalités de la V2.

| Méthode API V2   | Compatibilité API V3 |
|:----------- |:-------------|
| `Translate`     | [Translate](reference/v3-0-translate.md)          |
| `TranslateArray`      | [Translate](reference/v3-0-translate.md)        |
| `GetLanguageNames`      | [Langues](reference/v3-0-languages.md)         |
| `GetLanguagesForTranslate`     | [Langues](reference/v3-0-languages.md)       |
| `GetLanguagesForSpeak`      | [Service Microsoft Speech](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#text-to-speech)         |
| `Speak`     | [Service Microsoft Speech](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech)          |
| `Detect`     | [Detect](reference/v3-0-detect.md)         |
| `DetectArray`     | [Detect](reference/v3-0-detect.md)         |
| `AddTranslation`     | Cette fonctionnalité n’est plus prise en charge       |
| `AddTranslationArray`    | Cette fonctionnalité n’est plus prise en charge          |
| `BreakSentences`      | [BreakSentence](reference/v3-0-break-sentence.md)       |
| `GetTranslations`      | Cette fonctionnalité n’est plus prise en charge         |
| `GetTranslationsArray`      | Cette fonctionnalité n’est plus prise en charge         |

## <a name="move-to-json-format"></a>Passer au format JSON

L’API de traduction de texte Microsoft Translator Text V2 acceptait et renvoyait les données au format XML. Dans la V3, toutes les données envoyées et reçues à l’aide de l’API sont au format JSON. XML n’est plus accepté ou renvoyé dans la V3.

Cette modification affecte plusieurs aspects d’une application écrite pour l’API de traduction de texte V2. Par exemple : l’API Langues retourne des informations linguistiques pour la traduction de texte, la translittération et les deux méthodes de dictionnaire. Vous pouvez demander toutes les informations linguistiques pour toutes les méthodes dans un seul appel ou les demander individuellement.

La méthode Languages ne nécessite pas d’authentification ; en cliquant sur le lien suivant, vous pouvez voir toutes les informations linguistiques pour la V3 dans JSON :

[https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation,dictionary,transliteration](https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation,dictionary,transliteration)

## <a name="authentication-key"></a>Clé d’authentification

La clé d’authentification que vous utilisez pour la V2 sera acceptée pour la V3. Vous n’aurez pas besoin d’obtenir un nouvel abonnement. Vous serez en mesure de mélanger la V2 et la V3 dans vos applications pendant la période de migration d’un an, ce qui vous permettra de publier de nouvelles versions pendant la migration de la V2 (XML) à la V3 (JSON).

## <a name="pricing-model"></a>Modèle de tarification

La tarification de Microsoft Translator V3 est la même que celle de la V2 ; par caractère, espaces compris. Les nouvelles fonctionnalités de la V3 apportent des modifications dans les caractères comptabilisés pour la facturation.

| Méthode V3   | Caractères comptabilisés pour la facturation |
|:----------- |:-------------|
| `Languages`     | Aucun caractère envoyé, aucun comptabilisé, aucuns frais.          |
| `Translate`     | La comptabilisation se base sur le nombre de caractères envoyés pour traduction et dans combien de langues ils doivent être traduits. 50 caractères envoyés, 5 langues demandées, cela fait 50 x 5.           |
| `Transliterate`     | Le nombre de caractères envoyés pour translittération est comptabilisé.         |
| `Dictionary lookup & example`     | Le nombre de caractères envoyés pour la recherche dans le dictionnaire et les exemples est comptabilisé.         |
| `BreakSentence`     | Aucuns frais.       |
| `Detect`     | Aucuns frais.      |

## <a name="v3-end-points"></a>Points de terminaison V3

Globale

* api.cognitive.microsofttranslator.com

## <a name="v3-api-text-translations-methods"></a>Méthodes de traduction de texte de l’API V3

[`Languages`](reference/v3-0-languages.md)

[`Translate`](reference/v3-0-translate.md)

[`Transliterate`](reference/v3-0-transliterate.md)

[`BreakSentence`](reference/v3-0-break-sentence.md)

[`Detect`](reference/v3-0-detect.md)

[`Dictionary/lookup`](reference/v3-0-dictionary-lookup.md)

[`Dictionary/example`](reference/v3-0-dictionary-examples.md)

## <a name="compatibility-and-customization"></a>Compatibilité et personnalisation

> [!NOTE]
> 
> Microsoft Translator Hub seront supprimé le 17 mai 2019. [Afficher des dates et des informations sur la migration important](https://www.microsoft.com/translator/business/hub/).   

Microsoft Translator V3 utilise par défaut la traduction automatique neuronale. Par conséquent, il ne peut pas être utilisé avec Microsoft Translator Hub. Translator Hub prend uniquement en charge la traduction automatique statistique héritée. La personnalisation de la traduction neuronale est désormais disponible à l’aide du traducteur personnalisé. [En savoir plus sur la personnalisation de la traduction automatique neuronale](custom-translator/overview.md)

La traduction neuronale avec l'API de texte v3 ne prend pas en charge l'utilisation des catégories standard (SMT, speech, tech, generalnn).

| |Point de terminaison|    Conformité du processeur au RGPD|  Utiliser Translator Hub| Utiliser Custom Translator (préversion)|
|:-----|:-----|:-----|:-----|:-----|
|API de traduction de texte Translator Text version 2| api.microsofttranslator.com|    Non   |Oui    |Non |
|API de traduction de texte Translator Text version 3| api.cognitive.microsofttranslator.com|  Oui|    Non | Oui|

**API de traduction de texte Translator Text version 3**
* Est généralement disponible et entièrement prise en charge.
* Est conforme au RGPD en tant que processeur et remplit toutes les conditions de certification aux normes ISO 20001 et 20018 et SOC 3. 
* Vous permet d’appeler les systèmes de traduction de réseau neuronal que vous avez personnalisés avec Custom Translator (préversion), la nouvelle fonctionnalité de personnalisation Translator NMT. 
* Ne donne pas accès aux systèmes de traduction personnalisés créés à l’aide de Microsoft Translator Hub.

Vous utilisez la version 3 de l’API de traduction de texte Translator Text si vous utilisez le point de terminaison api.cognitive.microsofttranslator.com.

**API de traduction de texte Translator Text version 2**
* Ne remplit pas toutes les conditions de certification aux normes ISO 20001, 20018 et SOC 3. 
* Ne vous permet d’appeler les systèmes de traduction de réseau neuronal que vous avez personnalisés avec la fonctionnalité de personnalisation Translator.
* Donne accès aux systèmes de traduction personnalisés créés à l’aide de Microsoft Translator Hub.
* Vous utilisez la version 2 de l’API de traduction de texte Translator Text si vous utilisez le point de terminaison api.microsofttranslator.com.

Aucune version de l’API Translator ne crée un enregistrement de vos traductions. Vos traductions ne sont jamais partagées avec des tiers. Vous trouverez plus d’informations dans la page [Aucune trace](https://www.aka.ms/NoTrace) du site web Translator.

## <a name="links"></a>Liens

* [Politique de confidentialité Microsoft](https://privacy.microsoft.com/privacystatement)
* [Informations légales relatives à Microsoft Azure](https://azure.microsoft.com/support/legal)
* [Conditions d’utilisation des services en ligne](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Consulter la documentation de la V3.0](reference/v3-0-reference.md)
