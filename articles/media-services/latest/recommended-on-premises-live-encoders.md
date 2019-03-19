---
title: Encodeurs locaux de streaming en direct recommandés par Media Services – Azure | Microsoft Docs
description: Découvrez le streaming en direct d’encodeurs locaux recommandé par Media Services
services: media-services
keywords: encodage;encodeurs;média
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 01/17/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: cc55466dac7344053e0cce3cad5f8161cef5226a
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/06/2019
ms.locfileid: "57438123"
---
# <a name="recommended-live-streaming-encoders"></a>Encodeurs de streaming en direct recommandés

Dans Azure Media Services, un [Événement en temps réel](https://docs.microsoft.com/rest/api/media/liveevents) (canal) représente un pipeline de traitement du contenu de streaming en direct. L’Événement en temps réel reçoit les flux d’entrée en direct de l’une des deux manières suivantes.

* Un encodeur live local envoie à l'Événement en temps réel un flux RTMP ou Smooth Streaming (MP4 fragmenté) multidébit qui n'est pas activé pour effectuer un encodage en temps réel avec Media Services. Les flux ingérés transitent par les Événements en temps réel sans traitement supplémentaire. Cette méthode est appelée **pass-through**. Un encodeur live peut envoyer un flux à débit unique à un canal pass-through. Nous déconseillons cette configuration car elle n’offre pas de streaming à débit adaptatif au client.

  > [!NOTE]
  > L’utilisation d’une méthode pass-through est le moyen le plus économique de diffuser une vidéo en flux continu.

* Un encodeur live local envoie un flux à débit unique à l'Événement en temps réel activé pour effectuer un encodage en temps réel avec Media Services dans l'un des formats suivants : RTMP ou Smooth Streaming (MP4 fragmenté). L'Événement en temps réel procède ensuite à l'encodage en temps réel du flux à débit unique entrant en flux vidéo multidébit (adaptatif).

Pour des informations détaillées sur l’encodage en temps réel avec Media Services, voir [Streaming en direct avec Media Services v3](live-streaming-overview.md).

## <a name="live-encoders-that-output-rtmp"></a>Encodeurs live qui génèrent une sortie RTMP

Media Services recommande l’utilisation d’un des encodeurs live suivants, qui génèrent une sortie RTMP : Les schémas d’URL pris en charge sont `rtmp://` ou `rtmps://`.

> [!NOTE]
 > Lors de la diffusion en continu via RTMP, vérifiez les paramètres de pare-feu et/ou de proxy pour confirmer que les ports TCP sortants 1935 et 1936 sont ouverts.<br/>
 Lors de la diffusion en flux continu via RTMP, vérifiez les paramètres de pare-feu et/ou de proxy pour confirmer que les ports TCP sortants 2935 et 2936 sont ouverts.

- Adobe Flash Media Live Encoder 3.2
- Haivision KB
- Haivision Makito X HEVC
- OBS Studio
- Switcher Studio (iOS)
- Telestream Wirecast 8.1+
- Telestream Wirecast S
- Teradek Slice 756
- TriCaster 8000
- Tricaster Mini HD-4
- VMIX
- xStream

## <a name="live-encoders-that-output-fragmented-mp4"></a>Encodeurs live qui génèrent une sortie MP4 fragmenté

Media Services recommande l’utilisation d’un des encodeurs live suivants, qui génèrent une sortie Smooth Streaming multidébit (MP4 fragmenté). Les schémas d’URL pris en charge sont `http://` ou `https://`.

- Ateme TITAN Live
- Cisco Digital Media Encoder 2200
- Elemental Live
- Envivio 4Caster C4 Gen III
- Imagine Communications Selenio MCP3
- Media Excel Hero Live et Hero 4K (UHD/HEVC)

## <a name="configuring-on-premises-live-encoder-settings"></a>Configuration des paramètres de l'encodeur live local

Pour plus d'informations sur les paramètres valides pour votre type d'événement en temps réel, consultez [Comparaison des types d'événements en temps réel](live-event-types-comparison.md).

### <a name="playback-requirements"></a>Exigences de lecture

Pour pouvoir lire le contenu, un flux audio et un flux vidéo doivent être présents. La lecture de flux en vidéo uniquement n'est pas prise en charge.

### <a name="configuration-tips"></a>Conseils de configuration

- Si possible, utilisez une connexion Internet câblée.
- Lorsque vous déterminez les besoins en bande passante, doublez les débits binaires de diffusion en continu. Bien que facultative, cette règle simple permet de réduire l’impact de l’encombrement du réseau.
- Lors de l’utilisation d’encodeurs logiciels, fermez tous les programmes inutiles.
- La modification de la configuration de votre encodeur a des effets négatifs sur l’événement lorsque celui-ci a commencé à effectuer des transmissions de type push. Les modifications de configuration peuvent entraîner de l’instabilité. 
- Prévoyez suffisamment de temps pour configurer votre événement. Pour les événements à grande échelle, nous recommandons d’entamer la configuration une heure à l’avance.

## <a name="becoming-an-on-premises-encoder-partner"></a>Devenir un partenaire d’encodeur local

En tant que partenaire d’encodeur local d’Azure Media Services, Media Services promeut votre produit en recommandant votre encodeur aux clients d’entreprise. Pour devenir un partenaire d’encodeur local, vous devez vérifier la compatibilité de votre encodeur local avec Media Services. Pour ce faire, effectuez les vérifications suivantes.

### <a name="pass-through-live-event-verification"></a>Vérification d'un Événement en temps réel de type pass-through

1. Accédez à votre compte Media Services et vérifiez que le **point de terminaison de streaming** est en cours d’exécution. 
2. Créez et démarrez un Événement en temps réel de type **pass-through**. <br/> Pour plus d’informations, consultez [États et facturation des événements en direct](live-event-states-billing.md).
3. Récupérez les URL d’ingestion et configurez votre encodeur local de façon à ce qu’il utilise ces URL pour envoyer un flux temps réel multidébit à Media Services.
4. Récupérez l’URL d’aperçu et utilisez-la pour vérifier que l’entrée de l’encodeur est bien reçue.
5. Créez un objet **Asset**.
6. Créez un objet **LiveOutput** et utilisez le nom de l’objet Asset que vous venez de créer.
7. Créez un **localisateur de streaming**  avec les types intégrés de la **stratégie de streaming**.
8. Listez les chemins d'accès dans le **Localisateur de streaming** pour récupérer les URL à utiliser.
9. Récupérez le nom d’hôte du **Point de terminaison de streaming** à partir duquel vous souhaitez effectuer le streaming.
10. Combinez l’URL de l’étape 8 avec le nom d’hôte de l’étape 9 pour obtenir l’URL complète.
11. Exécutez votre encodeur live pendant environ 10 minutes.
12. Arrêtez l’événement en direct. 
13. Utilisez un lecteur, par exemple [Lecteur multimédia Azure](https://ampdemo.azureedge.net/azuremediaplayer.html) pour regarder l’élément multimédia archivé afin de vous assurer que la lecture est dépourvue de problèmes visibles à tous les niveaux de qualité. Vous pouvez également regarder et valider via l’URL de l’aperçu pendant la session active.
14. Enregistrez l’ID de la ressource, l’URL de streaming publié pour l’archive en temps réel, ainsi que les paramètres et la version utilisée à partir de votre encodeur live.
15. Réinitialisez l'état de l'Événement en temps réel après la création de chaque exemple.
16. Répétez les étapes 5 à 15 pour toutes les configurations prises en charge par votre encodeur (avec et sans signalisation des annonces, légendes ou vitesses d’encodage différentes).

### <a name="live-encoding-live-event-verification"></a>Vérification d'Événement en temps réel d'encodage en direct

1. Accédez à votre compte Media Services et vérifiez que le **point de terminaison de streaming** est en cours d’exécution. 
2. Créez et démarrez un Événement en temps réel d'**encodage en direct**. <br/> Pour plus d’informations, consultez [États et facturation des événements en direct](live-event-states-billing.md).
3. Récupérez les URL d’ingestion et configurez votre encodeur de façon à ce qu’il envoie (push) un flux temps réel à débit binaire à Media Services.
4. Récupérez l’URL d’aperçu et utilisez-la pour vérifier que l’entrée de l’encodeur est bien reçue.
5. Créez un objet **Asset**.
6. Créez un objet **LiveOutput** et utilisez le nom de l’objet Asset que vous venez de créer.
7. Créez un **localisateur de streaming**  avec les types intégrés de la **stratégie de streaming**.
8. Listez les chemins d'accès dans le **Localisateur de streaming** pour récupérer les URL à utiliser.
9. Récupérez le nom d’hôte du **Point de terminaison de streaming** à partir duquel vous souhaitez effectuer le streaming.
10. Combinez l’URL de l’étape 8 avec le nom d’hôte de l’étape 9 pour obtenir l’URL complète.
11. Exécutez votre encodeur live pendant environ 10 minutes.
12. Arrêtez l’événement en direct.
13. Utilisez un lecteur, par exemple [Lecteur multimédia Azure](https://ampdemo.azureedge.net/azuremediaplayer.html) pour regarder l’élément multimédia archivé afin de vous assurer que la lecture est dépourvue de problèmes visibles à tous les niveaux de qualité. Vous pouvez également regarder et valider via l’URL de l’aperçu pendant la session active.
14. Enregistrez l’ID de la ressource, l’URL de streaming publié pour l’archive en temps réel, ainsi que les paramètres et la version utilisée à partir de votre encodeur live.
15. Réinitialisez l'état de l'Événement en temps réel après la création de chaque exemple.
16. Répétez les étapes 5 à 15 pour toutes les configurations prises en charge par votre encodeur (avec et sans signalisation des annonces, légendes ou vitesses d’encodage différentes).

### <a name="longevity-verification"></a>Vérification de longévité

Suivez les mêmes étapes que pour la [Vérification d’un Événement en temps réel de type pass-through](#pass-through-live-event-verification), à l’exception de l’étape 11. <br/>Au lieu de 10 minutes, exécutez votre encodeur live pendant une semaine ou plus. Utilisez un lecteur, par exemple [Lecteur multimédia Azure](https://ampdemo.azureedge.net/azuremediaplayer.html) pour regarder de temps en temps le streaming en direct (ou un élément multimédia archivé) afin de vous assurer que la lecture est dépourvue de problèmes visibles.

### <a name="email-your-recorded-settings"></a>Envoi par e-mail de vos paramètres enregistrés

Enfin, envoyez par e-mail vos paramètres enregistrés et paramètres d’archivage en direct à Azure Media Services à l’adresse amsstreaming@microsoft.com en guise de notification indiquant que tous les contrôles de vérification automatique ont réussi. Incluez également vos informations de contact à des fins de suivi. Vous pouvez contacter l’équipe Azure Media Services pour toute question sur ce processus.

## <a name="next-steps"></a>Étapes suivantes

[Streaming en direct avec Media Services v3](live-streaming-overview.md)
