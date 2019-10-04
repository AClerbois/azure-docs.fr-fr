---
title: Comment fournir des commentaires sur des données à Azure Maps | Microsoft Docs
description: Fournissez des commentaires sur les données à l’aide de l’outil de commentaires Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 08/19/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 076f98cb240014bcc88a395902203413e31fe0f1
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2019
ms.locfileid: "69641869"
---
# <a name="provide-data-feedback-to-azure-maps"></a>Fournir des commentaires sur des données à Azure Maps

Azure Maps est généralement disponible depuis mai 2018. Il fournit des données cartographiques actualisées, des API REST faciles à utiliser, et des kits de développement logiciel (SDK) puissants pour aider nos entreprises clientes dans divers cas d’utilisation. Le monde réel change à chaque seconde et il est essentiel que nous puissions fournir une représentation numérique factuelle à nos clients. Nos clients qui prévoient d’ouvrir ou de fermer des installations doivent avoir la certitude que nos cartes sont rapidement actualisées, afin de pouvoir planifier efficacement la livraison, la maintenance ou le service clientèle dans des installations appropriées. Nous avons créé l’outil de commentaires sur les données d’Azure Maps pour permettre à nos clients de commenter les données directement. Les commentaires des clients sur les données sont directement transmis à nos fournisseurs de données et à leurs éditeurs de cartes, qui peuvent ainsi rapidement évaluer et intégrer ces commentaires dans nos produits de cartographie.  

L’[outil de commentaires sur les données d’Azure Maps](https://feedback.azuremaps.com) offre aux clients un moyen simple de commenter des données cartographiques, en particulier en lien avec des points d’intérêt et des adresses résidentielles. Cet article vous guide dans la manière de formuler différents types de commentaires à l’aide de l’outil de commentaires d’Azure Maps.

## <a name="add-a-business-place-or-a-residential-address"></a>Ajouter un emplacement commercial ou une adresse résidentielle 

Il se peut que vous vous vouliez signaler l’absence d’un point d’intérêt ou d’une adresse résidentielle sur une carte. Il y a deux façon de procéder. Vous pouvez ouvrir l’outil de commentaires sur les données d’Azure Maps, rechercher les coordonnées du lieu manquant, puis cliquer sur « Ajouter un emplacement ».

  ![rechercher un emplacement manquant](./media/how-to-use-feedback-tool/search-poi.png)

Ou vous pouvez interagir avec la carte, cliquer sur l’emplacement pour placer une épingle sur la coordonnée, puis cliquer sur « Ajouter un emplacement ». 

  ![ajouter une épingle](./media/how-to-use-feedback-tool/add-poi.png)

Lorsque vous cliquez sur cette commande, vous êtes redirigé vers un formulaire dans lequel vous pouvez fournir des détails sur l’emplacement.

  ![ajouter un emplacement](./media/how-to-use-feedback-tool/add-a-place.png)

## <a name="fix-a-business-place-or-a-residential-address"></a>Corriger un emplacement commercial ou une adresse résidentielle 

L’outil de commentaires vous permet également de rechercher et de localiser un emplacement commercial ou une adresse, puis de formuler un commentaire pour corriger l’adresse ou épingler l’emplacement s’ils ne sont pas corrects. Pour formuler un commentaire afin de corriger une adresse, utilisez la barre de recherche pour trouver l’emplacement commercial ou l’adresse résidentielle. Cliquez sur l’emplacement dans la liste des résultats, puis cliquez sur « Corriger cet emplacement ».

  ![rechercher un emplacement à corriger](./media/how-to-use-feedback-tool/fix-place.png)

Pour formuler un commentaire afin de corriger l’adresse, remplissez le formulaire « Corriger un emplacement », puis cliquez sur le bouton « Envoyer ».

  ![formulaire de correction](./media/how-to-use-feedback-tool/fix-form.png)

Si le positionnement de l’épingle indiquant l’emplacement est incorrect, dans le formulaire « Corriger un emplacement », activez la case à cocher libellée « L’emplacement de l’épingle est incorrect », déplacez l’épingle vers l’emplacement correct, puis cliquez sur le bouton « Envoyer ».

  ![déplacer une épingle](./media/how-to-use-feedback-tool/move-pin.png)

## <a name="add-a-comment"></a>Ajouter un commentaire 

En plus de vous permettre de rechercher un emplacement, l’outil de commentaires vous permet d’ajouter un du texte en forme libre pour fournir des détails sur l’emplacement. Pour ajouter un commentaire, recherchez l’emplacement ou cliquez dessus, cliquez sur « Ajouter un commentaire », rédigez un commentaire, puis cliquez sur « Envoyer ». 

  ![ajouter un commentaire](./media/how-to-use-feedback-tool/add-comment.png)

## <a name="track-status"></a>Suivi de l'état 

Vous pouvez également suivre le statut de votre demande en activant la case à cocher « Je veux suivre le statut » et en fournissant votre adresse e-mail lorsque vous créez une demande. Vous recevrez un lien de suivi dans l’e-mail notifiant l’état de votre demande. 

  ![état de commentaire](./media/how-to-use-feedback-tool/feedback-status.png)


## <a name="next-steps"></a>Étapes suivantes

Pour publier des questions techniques concernant Azure Maps, visitez le site :

* [Azure Maps Stack Overflow](https://stackoverflow.com/questions/tagged/azure-maps)
* [Forum de commentaires Azure Maps](https://feedback.azure.com/forums/909172-azure-maps)