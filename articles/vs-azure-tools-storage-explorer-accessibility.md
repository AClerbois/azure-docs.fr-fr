---
title: Accessibilité de l’Explorateur de stockage Azure (préversion) | Microsoft Docs
description: Accessibilité de l’Explorateur de stockage Azure (préversion)
services: storage
documentationcenter: na
author: MrayermannMSFT
manager: jinglouMSFT
editor: ''
ms.assetid: 1ed0f096-494d-49c4-ab71-f4164ee19ec8
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2018
ms.author: marayerm
ms.openlocfilehash: 617a5c35ce059bf13fcf0b5aab415d8e431041e0
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2018
ms.locfileid: "31525931"
---
# <a name="storage-explorer-accessibility"></a>Accessibilité de l’Explorateur Stockage Azure
## <a name="screen-readers"></a>Lecteurs d’écran
L’explorateur de stockage prend en charge l’utilisation d’un lecteur d’écran sur Windows et Mac. Les lecteurs d’écran suivants sont recommandés pour chaque plateforme :
* Windows : NVDA
* Mac : Voice Over
* Linux : Les lecteurs d’écran ne sont pas pris en charge sous Linux

Si vous rencontrez un problème d’accessibilité lors de l’utilisation de l’Explorateur de stockage, veuillez [ouvrir un problème sur GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues).

## <a name="zoom"></a>Zoom
Vous pouvez agrandir le texte dans l’Explorateur de stockage via un zoom avant. Pour effectuer un zoom, cliquez sur **Zoom avant** dans le menu d’aide. Vous pouvez également utiliser le menu d’aide pour effectuer un zoom arrière et rétablir le niveau de zoom à la valeur par défaut.

![Options de zoom dans le menu d’aide][0]

Le paramètre du zoom augmente la taille de la plupart des éléments de l’interface utilisateur. Il est recommandé d’activer également le texte de grande taille et les paramètres de zoom de votre système d’exploitation pour vous assurer que tous les éléments de l’interface utilisateur sont correctement mis à l’échelle.

## <a name="high-contrast-theming"></a>Thème à contraste élevé
L’explorateur de stockage possède deux thèmes à contraste élevé, **Contraste élevé (clair)** et **Contraste élevé (sombre)**. Vous pouvez modifier votre thème en accédant au sous-menu Thèmes dans le menu d’aide.

![Sous-menu Thèmes][1]

Le paramètre de thème change la couleur de la plupart des éléments de l’interface utilisateur. Il est recommandé d’activer également le thème à contraste élevé de votre système d’exploitation pour s’assurer que tous les éléments de l’interface utilisateur sont correctement colorés.

## <a name="shortcut-keys"></a>Touches de raccourci
### <a name="window-commands"></a>Commandes de la fenêtre
|Commande|Raccourci clavier|
|--------------|------------------------|
|Nouvelle fenêtre|**Ctrl + Maj + N**|
|Fermer l'éditeur|**Ctrl + F4**|
|Quitter|**Ctrl + Maj + W**|

### <a name="navigation-commands"></a>Commandes de navigation
|Commande|Raccourci clavier|
|--------------|------------------------|
|Focus sur le panneau suivant|**F6**|
|Focus sur le panneau précédent|**Maj + F6**|
|Explorer|**Ctrl + Maj + E**|
|Gestion de compte|**Ctrl + Maj + A**|
|Activer/désactiver la barre latérale|**Ctrl + B**|
|Journal d’activité|**Ctrl + Maj + L**|
|Actions et propriétés|**Ctrl + Maj + P**|
|Éditeur actuel|**Ctrl + Accueil**|
|Éditeur suivant|**Ctrl + Page suivante**|
|Éditeur précédent|**Ctrl + Page précédente**|

### <a name="zoom-commands"></a>Commandes Zoom
|Commande|Raccourci clavier|
|--------------|------------------------|
|Zoom avant|**Ctrl +=**|
|Zoom arrière|**Ctrl +-**|

### <a name="blob-and-file-share-editor-commands"></a>Commandes blob et commandes de l’éditeur de partage de fichiers
|Commande|Raccourci clavier|
|--------------|------------------------|
|Retour|**Alt + Flèche gauche**|
|Transférer|**Alt + Flèche droite**|
|Haut|**Alt + Flèche haut**|

### <a name="other-commands"></a>Autres commandes
|Commande|Raccourci clavier|
|--------------|------------------------|
|Copier|**Ctrl + C**|
|Couper|**Ctrl + X**|
|Coller|**Ctrl + V**|
|Activer/désactiver des outils de développement|**F12**|
|Recharger|**Ctrl + R**|

[0]: ./media/vs-azure-tools-storage-explorer-accessibility/Zoom.png
[1]: ./media/vs-azure-tools-storage-explorer-accessibility/HighContrast.png
