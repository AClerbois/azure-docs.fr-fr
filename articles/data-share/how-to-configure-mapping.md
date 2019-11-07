---
title: Configurer un mappage de jeu de données dans Azure Data Share
description: Découvrez comment configurer un mappage de jeu de données pour un partage reçu dans Azure Data Share.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 754977788c5f6e5e574500552f670ba9083cf683
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490605"
---
# <a name="how-to-configure-a-dataset-mapping-for-a-received-share-in-azure-data-share"></a>Comment configurer un mappage de jeu de données pour un partage reçu dans Azure Data Share

Cet article explique comment configurer un mappage de jeu de données pour un partage reçu à l’aide d’Azure Data Share. Vous souhaiterez le faire si vous avez accepté une invitation de partage de données mais que vous avez opté pour « Accepter et configurer ultérieurement ». Vous souhaiterez peut-être configurer un mappage de jeu de données si vous devez modifier la destination des données partagées avec vous ou si vous souhaitez recevoir des données dans un SQL Server. 

## <a name="navigate-to-a-received-data-share"></a>Accéder à un partage de données reçues

Dans le service Azure Data Share, accédez à votre partage reçu et sélectionnez l’onglet **Détails**. 

![Mappage de jeu de données](./media/dataset-mapping.png "Mappage de jeu de données") 

Cochez la case en regard du jeu de données auquel vous souhaitez affecter une destination. Sélectionnez **Démapper** pour annuler le mappage existant. Sélectionnez **+ Map to target** (+ Mapper sur cible) afin de choisir un nouveau magasin de destination. 

![Mapper sur cible](./media/dataset-map-target.png "Mapper sur cible") 

## <a name="select-a-new-destination-store"></a>Sélectionner un nouveau magasin de destination

Sélectionnez un type de données cibles dans lequel vous souhaitez que les données arrivent. Notez que toutes les données existant déjà dans les comptes de stockage précédemment mappés ne sont pas automatiquement déplacées vers la nouvelle destination.

![Compte de stockage cible](./media/dataset-map-target-sql.png "Stockage cible") 

## <a name="select-a-file-format-sql-sources-only"></a>Sélectionner un format de fichier (sources SQL uniquement)

Si les données sources proviennent d’une source basée sur SQL, vous pouvez choisir le format dans lequel elles sont reçues. 

![Choisir le format](./media/sql-file-formats.png "Formats de fichiers SQL")

## <a name="next-steps"></a>Étapes suivantes

Pour découvrir comment commencer à partager des données, passez au tutoriel [Partager vos données](share-your-data.md).



