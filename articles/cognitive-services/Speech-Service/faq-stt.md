---
title: Forum aux questions sur le service de reconnaissance vocale dans Azure
titleSuffix: Azure Cognitive Services
description: Découvrez les réponses aux questions les plus fréquemment posées sur le service de reconnaissance vocale.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
origin.date: 06/11/2018
ms.date: 04/01/2019
ms.author: v-biyu
ms.openlocfilehash: 29ec50a1d2e0aebb1aeb73b38c662f4a18624754
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60539058"
---
# <a name="speech-to-text-frequently-asked-questions"></a>Forum aux questions sur la reconnaissance vocale

Si vous ne trouvez pas de réponses à vos questions dans ce FAQ, consultez les [autres options de support](support.md).

## <a name="general"></a>Généralités

**Q : Quelle est la différence entre un modèle de reconnaissance vocale de référence et un modèle personnalisé ?**

**R** : Un modèle de référence est un modèle formé à l’aide de données appartenant à Microsoft et déjà déployé dans le cloud.  Un modèle personnalisé permet d’adapter un modèle à un environnement spécifique se singularisant par un bruit ambiant ou un langage particuliers. Un atelier, une voiture ou une rue bruyante nécessiteraient d’utiliser un modèle acoustique adapté. Des thèmes tels que la biologie, la physique et la radiologie, ou dans le cadre desquels sont utilisés des noms de produits et autres acronymes personnalisés nécessiteraient un modèle linguistique adapté.

**Q : Par où commencer si je souhaite utiliser un modèle de référence ?**

**R** : Commencez par obtenir une [clé d’abonnement](get-started.md). Si vous voulez adresser des appels REST à des modèles de référence pré-déployés, consultez les informations sur les [API REST](rest-apis.md). Si vous voulez utiliser des WebSockets, téléchargez le [Kit de développement logiciel (SDK)](speech-sdk.md).

**Q : Dois-je toujours générer un modèle vocal personnalisé ?**

**R** : Non. Si votre application utilise un langage générique, quotidien, vous n’avez pas besoin de personnaliser un modèle. Si elle est utilisée dans un environnement dénué ou presque de bruit de fond, ce n’est pas davantage nécessaire.

Vous pouvez déployer des modèles de référence et personnalisés dans le portail, puis exécuter des tests de précision sur ceux-ci. Vous pouvez utiliser cette fonctionnalité pour mesurer la précision d’un modèle de référence par rapport à celle d’un modèle personnalisé.

**Q : Comment savoir quand le traitement de mon jeu de données ou modèle est terminé ?**

**R** : Actuellement, la seule façon de le savoir est de consulter l’état du modèle ou du jeu de données dans le tableau. Une fois le traitement terminé, l’état est **Réussi**.

**Q : Puis-je créer plusieurs modèles ?**

**R** : Il n’existe aucune limite au nombre de modèles que vous pouvez avoir dans votre collection.

**Q : Je réalise que j’ai commis une erreur. Comment faire pour annuler une importation de données ou une création de modèle en cours ?**

**R** : Actuellement, vous ne pouvez pas restaurer un processus d’adaptation acoustique ou linguistique. Vous pouvez supprimer des données et modèles importés une fois que ceux-ci sont dans un état terminal.

**Q : Quelle est la différence entre les modèles Search (Recherche) ou Dictation (Dictée) et le modèle Conversational (Conversation) ?**

**R** : Le service vocal vous offre le choix entre plusieurs modèles de référence. Le modèle Conversational (Conversation) est adapté à la reconnaissance vocale de langage familier ou informel. Ce modèle est idéal pour la transcription d’appels téléphoniques. Les modèles Search (Recherche) et Dictation (Dictée) sont idéaux pour les applications déclenchées par la voix. Le modèle Universal (Universel) est un nouveau modèle destiné à couvrir les deux scénarios. Actuellement, le modèle Universal (Universel) répond ou dépasse le niveau de qualité du modèle Conversational (Conversation) dans la plupart des paramètres régionaux.

**Q : Puis-je mettre à jour un modèle existant (empilement de modèles) ?**

**R** : Vous ne pouvez pas mettre à jour un modèle existant. Une solution consiste à combiner l’ancien jeu de données avec le nouveau, puis à apporter les adaptions souhaitées.

L’ancien et le nouveau jeux de données doivent être combinés dans un seul fichier .zip (pour les données acoustiques) ou dans un fichier .txt (pour les données linguistiques). Une fois les adaptations apportées, le nouveau modèle mis à jour doit être redéployé afin d’obtenir un nouveau point de terminaison.

**Q : Quand une nouvelle version d’un modèle de référence est disponible, mon déploiement est-il mis à jour automatiquement ?**

**R** : Les déploiements ne sont PAS mis à jour automatiquement.

Si vous avez adapté et déployé un modèle de référence V1.0, ce déploiement reste en l’état. Les clients peuvent mettre hors service le modèle déployé, ré-adapter à l’aide de la version la plus récente de la ligne de base et déployez de nouveau.

**Q : Que faire si j’ai besoin pour mon modèle déployé d’une concurrence plus élevée que ce que propose le portail ?**

**R** : Vous pouvez augmenter l’échelle de votre modèle par incréments de 20 requêtes simultanées.

Si vous avez besoin d’augmenter l’échelle de votre modèle, contactez-nous.

**Q : Puis-je télécharger mon modèle et l’exécuter localement ?**

**R** : Il n’est pas possible de télécharger et d’exécuter des modèles localement.

**Q : Mes requêtes sont-elles journalisées ?**

**R** : Lorsque vous créez un déploiement, vous avez le choix de désactiver le traçage. Dans ce cas, les contenus audio et transcriptions ne sont pas journalisés. Autrement, les demandes sont généralement journalisées dans un stockage sécurisé dans Azure.

**Q : Mes requêtes sont-elles limitées ?**

**R** : L’API REST limite les requêtes à 25 par période de 5 secondes. Pour plus de détails, consultez nos pages sur la [Reconnaissance vocale](speech-to-text.md).

Si vous rencontrez des problèmes de confidentialité qui vous empêchent d’utiliser le service vocal personnalisé, contactez l’un des canaux de support.

## <a name="importing-data"></a>Importation de données

**Q : Quelle est la limite de taille d’un jeu de données, et pourquoi ?**

**R** : La limite de taille actuelle d’un jeu de données est de 2 Go. Cette limite découle de la restriction de taille de fichier pour le chargement HTTP. 

**Q : Puis-je zipper mes fichiers texte afin de charger des fichiers plus volumineux ?** 

**R** : Non. Seuls des fichiers texte non compressés sont actuellement autorisés.

**Q : Le rapport relatif aux données indique que des énoncés ont échoué. Quel est le problème ?**

**R** : L’échec du chargement de 100 % des énoncés dans un fichier n’est pas un problème. Si la majeure partie des énoncés d’un jeu de données acoustiques ou linguistiques (par exemple, plus de 95 %) est importée avec succès, le jeu de données est utilisable. Toutefois, nous vous recommandons d’essayer de comprendre les raisons pour lesquelles le chargement de ces énoncés a échoué et de résoudre les problèmes rencontrés. La plupart des problèmes courants, comme les erreurs de mise en forme, sont faciles à corriger. 

## <a name="creating-an-acoustic-model"></a>Création d’un modèle acoustique

**Q : De quel volume de données acoustiques ai-je besoin ?**

**R** : Nous vous recommandons de commencer avec entre 30 et 60 minutes de données acoustiques.

**Q : Quelles données dois-je collecter ?**

**R** : Collectez des données aussi proches que possible du scénario et du cas d’usage de l’application. La collection de données doit correspondre à l’application et aux utilisateurs cibles en termes d’appareils, d’environnements et de types d’orateurs. En général, vous devez collecter des données d’un éventail d’orateurs aussi large que possible. 

**Q : Comment collecter des données acoustiques ?**

**R** : Vous pouvez créer une application de collecte de données autonome, ou utiliser n’importe que logiciel d’enregistrement audio du commerce. Vous pouvez également créer une version de votre application qui journalise les données audio, puis les utilise. 

**Q : Dois-je me charger de la transcription des données d’adaptation ?**

**R** : Oui. Vous pouvez les transcrire vous-même ou utiliser un service de transcription professionnel. Certains utilisateurs se tournent vers des transcripteurs professionnels, d’autres optent pour une externalisation participative, et d’autres encore préfèrent transcrire eux-mêmes.

## <a name="accuracy-testing"></a>Tests de précision

**Q : Puis-je tester hors connexion mon modèle acoustique personnalisé à l’aide d’un modèle linguistique personnalisé ?**

**R** : Oui. Sélectionnez simplement le modèle linguistique personnalisé dans le menu déroulant quand vous configurez le test hors connexion.

**Q : Puis-je tester hors connexion mon modèle linguistique personnalisé à l’aide d’un modèle acoustique personnalisé ?**

**R** : Oui. Sélectionnez simplement le modèle acoustique personnalisé dans le menu déroulant quand vous configurez le test hors connexion.

**Q : Qu’est-ce que le taux d’erreur de mots et comment est-il calculé ?**

**R** : Le taux d’erreur de mots est la métrique d’évaluation de la reconnaissance vocale. Il est calculé comme le nombre total d’erreurs d’insertion, de suppression et de remplacement, divisé par le nombre total de mots présents dans la transcription de référence. Pour plus d’informations, voir [taux d’erreur de mots](https://en.wikipedia.org/wiki/Word_error_rate).

**Q : Comment faire pour déterminer si les résultats d’un test de précision sont bons ?**

**R** : Les résultats sont le fruit d’une comparaison entre le modèle de référence et le modèle personnalisé. Pour que la personnalisation soit pertinente, l’objectif doit être de surpasser le modèle de référence.

**Q : Comment faire pour déterminer le taux d’erreur de mots d’un modèle de référence afin de voir s’il y a une amélioration ?** 

**R** : Les résultats de test hors connexion indiquent la précision de référence du modèle personnalisé, et l’amélioration obtenue par rapport à cette référence.

## <a name="creating-a-language-model"></a>Création d’un modèle linguistique

**Q : Quel volume de données de texte dois-je charger ?**

**R** : Cela dépend de la mesure dans laquelle la terminologie et les expressions spécifiques utilisées dans votre application se distinguent des modèles linguistiques de départ. Pour tous les nouveaux mots, il est utile de fournir autant d’exemples que possible de l’utilisation de ces mots. Il est utile d’inclure dans les données linguistiques les expressions couramment utilisées au sein de votre application, car cela indique au système d’écouter également ces termes. Il est courant d’avoir au moins une centaine, et généralement plusieurs centaines, d’énoncés dans le jeu de données linguistiques. Par ailleurs, si certains types de requêtes sont censés être plus fréquents que d’autres, vous pouvez insérer plusieurs copies des requêtes courantes dans le jeu de données.

**Q : Puis-je simplement charger une liste de mots ?**

**R** : Le chargement d’une liste de mots a pour effet d’ajouter des mots au vocabulaire, mais n’enseigne pas au système la manière dont ceux-ci sont généralement utilisés. En fournissant des énoncés complets ou partiels (phrases ou expressions que les utilisateurs sont susceptibles de prononcer), le modèle linguistique peut apprendre les mots nouveaux ainsi que la façon dont ils sont utilisés. Le modèle linguistique personnalisé convient, non seulement pour l’ajout de mots au système, mais aussi pour l’ajustement de la probabilité d’occurrence de mots connus pour votre application. Fournir des énoncés complets permet au système de mieux apprendre. 

## <a name="next-steps"></a>Étapes suivantes

* [Dépannage](troubleshooting.md)
* [Notes de publication](releasenotes.md)
