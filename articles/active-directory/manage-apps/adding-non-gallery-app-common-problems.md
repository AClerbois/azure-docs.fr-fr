---
title: Problème d’ajout d’une application hors galerie | Microsoft Docs
description: Comprendre les problèmes courants auxquels les utilisateurs sont confrontés lors de l'ajout d'applications hors galerie personnalisées
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/11/2018
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: ce103ca6c958b0524d753da25c2a79aadfb7c8b3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84760284"
---
# <a name="problem-adding-a-non-gallery-application"></a>Problème d’ajout d’une application hors galerie

Cet article vous aide à comprendre les problèmes courants auxquels les utilisateurs sont confrontés lors de l’ajout **d’applications hors galerie personnalisées** et ce que vous pouvez faire pour les résoudre. 

## <a name="i-clicked-the-add-button-and-my-application-took-a-long-time-to-appear"></a>J’ai cliqué sur le bouton « Ajouter » et l’application a mis beaucoup de temps à s’afficher

Dans certaines conditions, 1 à 2 minutes (et parfois plus) peuvent être nécessaires pour qu’une application apparaisse après avoir été ajoutée à votre annuaire. Même si ce comportement demeure anormal, vous pouvez visualiser la progression de l’ajout de l’application en cliquant sur l’icône **Notifications** (cloche) dans le coin supérieur droit du [portail Azure](https://portal.azure.com/) et en recherchant la notification **En cours** ou **Terminé** pour le champ **Créer une application**.

Si votre application n’est jamais ajoutée, ou si vous rencontrez une erreur lorsque vous cliquez sur le bouton **Ajouter**, une **notification** affiche l’état **Erreur**. Si vous souhaitez obtenir plus de détails sur l’erreur afin d’en savoir plus sur ou de partager ces informations avec un ingénieur du support, vous pouvez afficher plus d’informations sur l’erreur en suivant les étapes décrites dans la section [Guide pratique pour afficher les détails d’une notification du portail](#how-to-see-the-details-of-a-portal-notification).

## <a name="i-clicked-the-add-button-and-my-application-didnt-appear"></a>J’ai cliqué sur le bouton « Ajouter » et mon application n’apparaît pas

En raison de problèmes temporaires, de problèmes de réseau ou d’un bogue, l’ajout d’une application peut parfois échouer. Ce problème se manifeste lorsque vous cliquez sur l’icône **Notifications** (cloche) dans le coin supérieur droit du portail Azure et qu’une icône rouge (!) apparaît en regard de votre notification **Créer une application**. Cela indique qu’une erreur est survenue lors de la création de l’application.

Si vous rencontrez une erreur lorsque vous cliquez sur le bouton **Ajouter**, une **notification** affiche l’état **Erreur**. Si vous souhaitez obtenir plus de détails sur l’erreur afin d’en savoir plus sur ou de partager ces informations avec un ingénieur du support, vous pouvez afficher plus d’informations sur l’erreur en suivant les étapes décrites dans la section [Guide pratique pour afficher les détails d’une notification du portail](#how-to-see-the-details-of-a-portal-notification).

## <a name="i-dont-know-how-to-set-up-my-application-once-ive-added-it"></a>Je ne sais pas comment configurer mon application une fois que je l’ai ajoutée

Si vous avez besoin d’aide pour en savoir plus sur les applications personnalisées, la [bibliothèque de documents Applications Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-apps-index) vous fournit plus d’informations sur l’authentification unique avec Azure AD et son fonctionnement.

## <a name="how-to-see-the-details-of-a-portal-notification"></a>Comment afficher les détails d’une notification du portail

Vous pouvez afficher les détails d’une notification provenant du portail en suivant les étapes ci-dessous :

1. Cliquez sur l’icône **Notifications** (cloche) dans le coin supérieur droit du portail Azure.

2. Sélectionnez n’importe quelle notification avec l’état **Erreur**, signalée par un (!) rouge.

   >[!NOTE]
   >Vous ne pouvez pas cliquer sur les notifications affichant l’état **Réussi** ou **En cours**.
   >
   >

4. Utilisez les informations sous **Détails de la notification** pour comprendre plus de détails sur le problème.

5. Si vous avez encore besoin d’aide, vous pouvez également partager ces informations avec un ingénieur du support technique ou du groupe de produits qui vous assistera pour résoudre votre problème.

6. Cliquez sur l **’icône de copie** à droite de la zone de texte **Erreur de copie** pour copier tous les détails de la notification afin de les partager avec un ingénieur du support technique ou du groupe de produits.

## <a name="how-to-get-help-by-sending-notification-details-to-a-support-engineer"></a>Comment obtenir de l’aide en envoyant les détails de la notification à un ingénieur du support technique

Il est très important que vous partagiez **tous les détails ci-dessous** avec un ingénieur du support technique si vous avez besoin d’aide, car celui-ci peut vous permettre de résoudre rapidement le problème. Vous pouvez facilement le faire en **prenant une capture d’écran** ou en cliquant sur **l’icône d’erreur de copie** située à droite de la zone de texte **Erreur de copie**.

## <a name="notification-details-explained"></a>Explication des détails de la notification

Consultez les descriptions suivantes pour plus d’informations sur les notifications.

### <a name="essential-notification-items"></a>Éléments essentiels de la notification

- **Titre** : titre descriptif de la notification
  *  Exemple : **Paramètres de proxy d’application**

- **Description** : description de ce qui s’est produit suite à l’opération

  *  Exemple : **L’URL interne entrée est déjà utilisée par une autre application**

- **ID de notification** : ID unique de la notification

  *  Exemple : **clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068**

- **ID de demande client** : ID de la demande spécifique effectuée par votre navigateur

  *  Exemple : **302fd775-3329-4670-a9f3-bea37004f0bc**

- **Horodatage UTC** : horodatage de l’affichage de la notification, au format UTC

  *  Exemple : **2017-03-23T19:50:43.7583681Z**

- **ID de transaction interne** : ID interne nous permettant de rechercher l’erreur dans nos systèmes

  *  Exemple : **71a2f329-ca29-402f-aa72-bc00a7aca603**

- **UPN** : utilisateur qui a effectué l’opération

  *  Exemple : **tperkins\@f128.info**

- **ID de locataire** : ID unique du locataire dont l’utilisateur ayant effectué l’opération était membre

  *  Exemple : **7918d4b5-0442-4a97-be2d-36f9f9962ece**

- **ID de l’objet utilisateur** : ID unique de l’utilisateur ayant effectué l’opération

  *  Exemple : **17f84be4-51f8-483a-b533-383791227a99**

### <a name="detailed-notification-items"></a>Éléments détaillés de la notification

- **Nom d’affichage** : **(peut être vide)** nom d’affichage plus détaillé de l’erreur

  *  Exemple : **Paramètres de proxy d’application**

- **État** : état spécifique de la notification

  *  Exemple : **Échec**

- **ID de l’objet** : **(peut être vide)** ID de l’objet sur lequel l’opération a été effectuée

  *  Exemple : **8e08161d-f2fd-40ad-a34a-a9632d6bb599**

- **Détails** : description détaillée de ce qui s’est produit suite à l’opération

  *  Exemple : **L’URL interne `https://bing.com/` n’est pas valide car elle est déjà en cours d’utilisation**

- **Erreur de copie** : cliquez sur l **’icône de copie** à droite de la zone de texte **Erreur de copie** pour copier tous les détails de la notification afin de les partager avec le support technique ou du groupe de produits 
- ingénierie

  *  Exemple ```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'https://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'https://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```




