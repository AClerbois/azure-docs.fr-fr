---
title: 'Se connecter à Windows Virtual Desktop à partir de macOS : Azure'
description: Comment se connecter à Windows Virtual Desktop à l’aide du client macOS.
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: helohr
ms.openlocfilehash: dc980d60c1db684a47c38b3b8efceb08dd618838
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73605800"
---
# <a name="connect-with-the-macos-client"></a>Se connecter avec le client macOS

> S’applique à : macOS 10.12 ou version ultérieure

>[!NOTE]
> L’accès aux ressources Windows Virtual Desktop à partir du client macOS est disponible en préversion.

Vous pouvez accéder aux ressources Windows Virtual Desktop à partir de vos appareils macOS avec notre client téléchargeable. Ce guide vous explique comment configurer le client.

## <a name="install-the-client"></a>Installation du client

Pour commencer, [téléchargez](https://apps.apple.com/app/microsoft-remote-desktop/id1295203466?mt=12) et installez le client sur votre appareil macOS.

## <a name="subscribe-to-a-feed"></a>S’abonner à un flux

Abonnez-vous au flux que votre administrateur vous a indiqué pour obtenir la liste des ressources managées disponibles sur votre appareil macOS.

Pour s’abonner à un flux :

1. Sélectionnez **Add Feed** (Ajouter un flux) sur la page principale pour vous connecter au service et récupérer vos ressources.
2. Entrez l’URL du flux. Il peut s’agir d’une URL ou d’une adresse e-mail :
   - Si vous utilisez une URL, utilisez celle que votre administrateur vous a donnée. Normalement, l’URL est <https://rdweb.wvd.microsoft.com>.
   - Pour utiliser l’e-mail, entrez votre adresse e-mail. Cela indique au client de rechercher une URL associée à votre adresse e-mail si votre administrateur a configuré le serveur de cette manière.
3. Sélectionnez **Subscribe** (S’abonner).
4. Connectez-vous avec votre compte d’utilisateur quand vous y êtes invité.

Une fois connecté, vous devez voir la liste des ressources disponibles.

Une fois que vous êtes abonné à un flux, son contenu est mis à jour automatiquement et régulièrement. Des ressources peuvent être ajoutées, modifiées ou supprimées en fonction des modifications apportées par votre administrateur.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur l’utilisation du client macOS, consultez [Bien démarrer avec le client macOS](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-mac).
