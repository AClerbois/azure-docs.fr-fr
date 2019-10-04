---
title: Événement en direct Azure Media Services et magnétoscope numérique cloud | Microsoft Docs
description: Cet article explique en quoi consiste une sortie en direct et comment utiliser un magnétoscope numérique cloud.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 08/27/2019
ms.author: juliako
ms.openlocfilehash: a10c76dd7fb4ef1e9a45666ff3a3ca0d937d2c94
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/03/2019
ms.locfileid: "70231228"
---
# <a name="using-a-cloud-digital-video-recorder-dvr"></a>Utilisation d’un magnétoscope numérique cloud

Dans Azure Media Services, un objet [Sortie en direct](https://docs.microsoft.com/rest/api/media/liveoutputs) fonctionne comme un magnétoscope numérique qui capture et enregistre votre stream en direct dans un élément multimédia dans votre compte Media Services. Le contenu enregistré est conservé dans le conteneur défini par la ressource [Élément multimédia](https://docs.microsoft.com/rest/api/media/assets) (le conteneur est dans le compte de stockage Azure associé à votre compte). La sortie en direct vous permet également de contrôler certaines propriétés du stream en direct sortant, notamment la quantité du flux conservée dans l’enregistrement archive (par exemple, la capacité du magnétoscope numérique cloud) et le fait que les destinataires sont autorisés ou non à démarrer la lecture du stream en direct. L’archive sur le disque est une archive circulaire de type « fenêtre » qui stocke uniquement la quantité de contenu spécifiée dans la propriété **archiveWindowLength** de la sortie en direct. Le contenu qui dépasse cette fenêtre d’archive est automatiquement supprimé du conteneur de stockage et n’est pas récupérable. La valeur archiveWindowLength représente un intervalle de temps ISO-8601 (par exemple, PTHH:MM:SS), qui spécifie la capacité du magnétoscope numérique. Sa valeur est comprise entre 3 minutes et 25 heures.

La relation entre un événement en direct et ses sorties en direct est comparable à la diffusion télévisuelle traditionnelle, où un canal (événement en direct) représente un flux vidéo constant et un enregistrement (sortie en direct) est limité à une plage horaire spécifique (par exemple, un journal télévisé de 18h30 à 19h). Une fois que le flux transite dans l’événement en direct, vous pouvez commencer l’événement de streaming en créant un élément multimédia, une sortie en direct et un localisateur de streaming. La sortie en direct archive le flux et le met à la disposition des observateurs via le [point de terminaison de streaming](https://docs.microsoft.com/rest/api/media/streamingendpoints). Vous pouvez créer plusieurs sorties en direct (trois maximum) sur un événement en direct avec différents paramètres et longueurs de fenêtre d’archive. Pour plus d’informations sur le flux de travail de streaming en direct, vois la section [Étapes générales](live-streaming-overview.md#general-steps).

## <a name="using-a-dvr-during-an-event"></a>Utilisation d’un magnétoscope numérique pendant un événement 

Cette section explique comment utiliser un magnétoscope numérique au cours d’un événement afin de contrôler les parties du flux disponibles pour « rembobiner ».

La valeur ArchiveWindowLength détermine dans quelle mesure l’utilisateur peut revenir en arrière à partir de la position actuelle. La valeur ArchiveWindowLength spécifie également la longueur maximale que peuvent atteindre les manifestes du client.

Prenons l’exemple d’un match de football en streaming, dont la valeur ArchiveWindowLength n’est que de 30 minutes. Un utilisateur qui commencerait à regarder l’événement 45 minutes après le début de la rencontre pourrait revenir au maximum à la marque de 15 minutes. Les sorties en direct du match se poursuivent jusqu’à l’arrêt de l’événement en direct, mais le contenu situé en dehors de la propriété ArchiveWindowLength est systématiquement ignoré par le stockage et n’est pas récupérable. Dans cet exemple, la vidéo qui se trouve entre le début de l’événement et la marque de 15 minutes aurait été éliminée de votre magnétoscope numérique et du conteneur dans le Stockage Blob de l’élément multimédia. L’archive, non récupérable, est supprimée du conteneur dans le Stockage Blob Azure.

Un événement en direct prend en charge jusqu’à trois sorties en direct simultanées, ce qui rend possible la création de trois enregistrements/archives au maximum à partir d’un même flux en direct simultanément. Cela vous permet de publier et d’archiver différentes parties d’un événement en fonction des besoins. Supposons que vous vouliez diffuser un flux linéaire temps réel 24 h/24, 7 j/7 et créer tout au long de la journée des « enregistrements » des différents programmes, qui seront proposés à la demande aux utilisateurs dans le cadre d’un service de replay. Dans ce scénario, vous commencez par créer une sortie en direct principale, avec une fenêtre d’archive courte d’une heure ou moins : ce sera le stream en direct principal que visionneront les utilisateurs. Vous créez ensuite un localisateur de streaming pour cette sortie en direct et le publiez sur votre application ou votre site web comme flux « temps réel ». Pendant que l’événement en direct est en cours d’exécution, vous pouvez créer par programmation une deuxième sortie en direct simultanée au début d’un programme (ou 5 minutes avant pour fournir des descripteurs à supprimer ultérieurement). Cette deuxième sortie en direct peut être supprimée 5 minutes après la fin du programme. Avec ce second élément multimédia, vous pouvez créer un localisateur de streaming pour publier ce programme sous forme d’élément multimédia à la demande dans le catalogue de votre application. Vous pouvez répéter plusieurs fois ce processus pour les autres limites ou points forts du programme que vous souhaitez partager sous forme de vidéos à la demande, tandis que le flux « temps réel » de la première sortie en direct continue de diffuser le flux linéaire. 

## <a name="creating-an-archive-for-on-demand-playback"></a>Création d’une archive pour une lecture à la demande

La ressource sur laquelle la sortie dynamique est archivée devient automatiquement une ressource à la demande lorsque la sortie dynamique est supprimée. Vous devez supprimer toutes les sorties dynamiques avant de pouvoir arrêter un événement en direct. Vous pouvez éventuellement utiliser un indicateur [removeOutputsOnStop](https://docs.microsoft.com/rest/api/media/liveevents/stop#request-body) pour supprimer automatiquement les sorties dynamiques à l’arrêt. 

Même après l’arrêt et la suppression de l’événement, les utilisateurs pourront lire votre contenu archivé en tant que vidéo à la demande tant que vous n’aurez pas supprimé l’élément multimédia. Un élément multimédia ne doit pas être supprimé s’il est utilisé par un événement ; vous devez d’abord supprimer l’événement.

Si vous avez publié l’élément multimédia de votre sortie en direct à l’aide d’un localisateur de streaming, l’événement en direct (jusqu’à la longueur de la fenêtre DVR) restera visible jusqu’à l’expiration ou la suppression du localisateur de streaming, en fonction de ce qui se produit en premier.

Pour plus d'informations, consultez les pages suivantes :

- [Vue d’ensemble du streaming en direct](live-streaming-overview.md)
- [Didacticiel sur le streaming en direct](stream-live-tutorial-with-api.md)

> [!NOTE]
> Quand vous supprimez la sortie en direct, vous ne supprimez pas l’élément multimédia sous-jacent ni le contenu de celui-ci. 

## <a name="next-steps"></a>Étapes suivantes

* [Créez des sous-clips de vos vidéos](subclip-video-rest-howto.md).
* [Définissez des filtres pour vos éléments multimédias](filters-dynamic-manifest-rest-howto.md).
