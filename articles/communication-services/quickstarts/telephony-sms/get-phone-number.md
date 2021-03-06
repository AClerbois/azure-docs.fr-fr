---
title: Démarrage rapide - Obtenir un numéro de téléphone à partir d’Azure Communication Services
description: Découvrez comment acheter un numéro de téléphone Communication Services à l’aide du portail Azure.
author: ddematheu2
manager: nimag
services: azure-communication-services
ms.author: dademath
ms.date: 07/09/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.custom: references_regions
ms.openlocfilehash: 2743dc0164f604c9c5e033aacc3e58fae42a1fd2
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90944890"
---
# <a name="quickstart-get-a-phone-number-using-the-azure-portal"></a>Démarrage rapide : Obtenir un numéro de téléphone à l’aide du portail Azure

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Commencez avec Azure Communication Services en utilisant le portail Azure pour acheter un numéro de téléphone.

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Une ressource Communication Services active.](../create-communication-resource.md)

## <a name="get-a-phone-number"></a>Obtenir un numéro de téléphone

Pour commencer le provisionnement de numéros, accédez à votre ressource Communication Services sur le [portail Azure](https://portal.azure.com).

:::image type="content" source="../media/manage-phone-azure-portal-start.png" alt-text="Capture d’écran montrant la page principale d’une ressource Communication Services.":::

### <a name="getting-new-phone-numbers"></a>Obtenir de nouveaux numéros de téléphone

Accédez au panneau Numéros de téléphone dans le menu des ressources.

:::image type="content" source="../media/manage-phone-azure-portal-phone-page.png" alt-text="Capture d’écran montrant la page des numéros de téléphone d’une ressource Communication Services.":::

Appuyez sur le bouton `Get` pour lancer l’Assistant. L’Assistant sur le panneau `Phone numbers` vous présente une série de questions qui vous aideront à choisir le numéro de téléphone qui correspond le mieux à votre scénario. 

Vous devez d’abord choisir le pays ou la région (`Country/region`) où vous souhaitez provisionner le numéro de téléphone. Après avoir sélectionné le pays ou la région, vous devrez sélectionner le forfait téléphonique (`phone plan`) qui répond le mieux à vos besoins. 

:::image type="content" source="../media/manage-phone-azure-portal-get-numbers.png" alt-text="Capture d’écran montrant la vue Obtenir des numéros de téléphone.":::

### <a name="select-a-phone-plan"></a>Sélectionner un forfait téléphonique

La sélection d’un forfait téléphonique se décompose en deux étapes : 

1. La sélection du [Type de numéro](../../concepts/telephony-sms/plan-solution.md#phone-number-types-in-azure-communication-services)
2. La sélection du [forfait](../../concepts/telephony-sms/plan-solution.md#plans)

Nous proposons actuellement deux types de numéro : `Geographic` et `Toll-free`. Une fois que vous avez sélectionné un Type de numéro, plusieurs Forfaits parmi lesquels vous pouvez effectuer un choix vous sont proposés.

> [!NOTE]
> Actuellement, nous ne prenons en charge que la sélection de numéros de téléphone avec appels entrants OU sortants. Toutefois, vous pouvez acheter un numéro de téléphone avec les appels entrants activés, puis configurer l’ID d’appelant sortant pour qu’il corresponde au numéro de téléphone autorisant les appels entrants (ce que les utilisateurs voient quand vous les appelez à partir de votre application Communication Services).
> Cela s’applique uniquement aux communications bidirectionnelles. Le SMS bidirectionnel est pris en charge en mode natif.

Dans notre exemple, nous avons choisi un type de numéro `Toll-free` avec le forfait `Outbound calling`.

:::image type="content" source="../media/manage-phone-azure-portal-select-plans.png" alt-text="Capture d’écran montrant la vue Sélectionner des forfaits.":::

### <a name="declare-purpose"></a>Déclarer un objectif

Ensuite, l’Assistant vous demande l’objectif de l’utilisation du numéro. Nous collectons ces informations pour appliquer les réglementations fiscales et d’appels d’urgence appropriées.

:::image type="content" source="../media/quickstart-search-and-acquire-bot-or-human.png" alt-text="Capture d’écran montrant la sélection d’un bot ou d’un utilisateur humain au cours du processus d’acquisition d’un numéro de téléphone.":::

À partir de là, cliquez sur le bouton `Next: Numbers` au bas de la page pour personnaliser le ou les numéros de téléphone que vous souhaitez provisionner.

### <a name="customizing-phone-numbers"></a>Personnalisation de numéros de téléphone

Dans la page `Numbers`, vous allez personnaliser le ou les numéros de téléphone que vous souhaitez provisionner.

:::image type="content" source="../media/manage-phone-azure-portal-select-numbers-start.png" alt-text="Capture d’écran montrant la page de sélection des numéros.":::

> [!NOTE]
> Ce guide de démarrage rapide présente le flux de personnalisation du type de numéro `Toll-free`. L’expérience peut être légèrement différente si vous avez choisi le type de numéro `Geographic`, mais le résultat final sera le même.

Choisissez l’indicatif régional (`Area code`) dans la liste des indicatifs régionaux disponibles, entrez la quantité que vous souhaitez provisionner, puis cliquez sur `Search` (Rechercher) pour rechercher les numéros qui répondent aux spécifications sélectionnées. Les numéros de téléphone qui répondent à vos besoins s’affichent avec leur coût mensuel.

:::image type="content" source="../media/manage-phone-azure-portal-found-numbers.png" alt-text="Capture d’écran montrant la page de sélection des numéros avec des numéros réservés.":::

> [!NOTE]
> La disponibilité dépend du type de numéro, de l’emplacement et du forfait que vous avez sélectionnés.
> Les numéros sont réservés pendant une courte durée avant que la transaction n’expire. Si la transaction expire, vous devez resélectionner les numéros.

Pour afficher le récapitulatif de l’achat et passer votre commande, cliquez sur le bouton `Next: Summary` (Suivant : Récapitulatif) situé au bas de la page.

### <a name="place-order"></a>Passer une commande

La page récapitulative passe en revue le type de numéro, les fonctionnalités, les numéros de téléphone et le coût mensuel total sélectionnés pour provisionner les numéros de téléphone.

> [!NOTE]
> Les tarifs indiqués sont les **frais récurrents mensuels** qui couvrent le coût de location du numéro de téléphone sélectionné pour vous. Cette vue n’inclut pas les **coûts du paiement à l’utilisation** qui sont engendrés quand vous passez ou recevez des appels. Les tarifs sont [disponibles ici](../../concepts/pricing.md). Ces coûts dépendent du type de numéro et des destinations appelées. Par exemple, un tarif à la minute pour un appel d’un numéro régional de Seattle à un numéro régional à New York et pour un appel du même numéro à un numéro de téléphone mobile britannique peut être différent.

Enfin, cliquez sur `Place order` (Passer la commande) en bas de la page pour confirmer.

:::image type="content" source="../media/manage-phone-azure-portal-get-numbers-summary.png" alt-text="Capture d’écran montrant la page récapitulative avec le type de numéro, les fonctionnalités, les numéros de téléphone et le coût mensuel total affichés.":::

## <a name="find-your-phone-numbers-on-the-azure-portal"></a>Rechercher vos numéros de téléphone sur le portail Azure

Accédez à votre ressource Azure Communication sur le [portail Azure](https://portal.azure.com) :

:::image type="content" source="../media/manage-phone-azure-portal-start.png" alt-text="Capture d’écran montrant la page principale d’une ressource Communication Services.":::

Sélectionnez l’onglet Phone Numbers (Numéros de téléphone) dans le menu pour gérer vos numéros de téléphone.

:::image type="content" source="../media/manage-phone-azure-portal-phones.png" alt-text="Capture d’écran montrant la page des numéros de téléphone d’une ressource Communication Services.":::

> [!NOTE]
> L’affichage des numéros provisionnés sur cette page peut prendre quelques minutes.

## <a name="troubleshooting"></a>Dépannage

Questions et problèmes courantes :

- Seuls les États-Unis et le Canada prennent en charge l’achat de numéros de téléphone pour le moment. Celui-ci est basé sur l’adresse de facturation de l’abonnement auquel la ressource est associée. Actuellement, vous ne pouvez pas déplacer la ressource vers un autre abonnement.

- Quand un numéro de téléphone est supprimé, il n’est pas libéré ni être racheté avant la fin du cycle de facturation.

- Quand une ressource Communication Services est supprimée, les numéros de téléphone associés à cette ressource sont, dans le même temps, automatiquement libérés.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris comment :

> [!div class="checklist"]
> * Acheter un numéro de téléphone
> * Gérer votre numéro de téléphone
> * Libérer un numéro de téléphone

> [!div class="nextstepaction"]
> [Envoyer un SMS](../telephony-sms/send.md)
> [Bien démarrer avec les appels](../voice-video-calling/getting-started-with-calling.md)
