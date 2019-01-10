---
title: Charger des fichiers dans un compte Azure Media Services à l’aide de REST | Microsoft Docs
description: Apprenez à obtenir du contenu multimédia dans Media Services en créant et en chargeant des ressources.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/18/2018
ms.author: juliako
ms.openlocfilehash: f7615911d39ea66cdcf5ed9913ac420f2d1c9de9
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/19/2018
ms.locfileid: "53636116"
---
# <a name="upload-files-into-a-media-services-account-using-rest"></a>Charger des fichiers dans un compte Media Services à l’aide de REST

Dans Media Services, vous chargez vos fichiers numériques dans un conteneur d'objets blob associé à une ressource. L’entité [Asset](https://docs.microsoft.com/rest/api/media/operations/asset) peut contenir des fichiers vidéo et audio, des images, des collections de miniatures, des pistes textuelles et des légendes (et les métadonnées concernant ces fichiers). Une fois les fichiers chargés dans le conteneur de la ressource, votre contenu est stocké en toute sécurité dans le cloud et peut faire l'objet d'un traitement et d'une diffusion en continu.

Cet article explique comment charger un fichier local à l'aide de REST.

## <a name="prerequisites"></a>Prérequis

Pour suivre les étapes décrites dans cette rubrique, vous devez :

- Revoir le [concept de ressource](assets-concept.md).
- [Configurer Postman pour les appels d’API REST Azure Media Services](media-rest-apis-with-postman.md).
    
    Suivez la dernière étape de la rubrique [Obtenir un jeton Azure AD](media-rest-apis-with-postman.md#get-azure-ad-token). 

## <a name="create-an-asset"></a>Créer une ressource

Cette section explique comment créer une nouvelle ressource.

1. Sélectionnez **Ressources** -> **Créer ou mettre à jour une ressource**.
2. Appuyez sur **Envoyer**.

    ![Créer une ressource](./media/upload-files/postman-create-asset.png)

La **Réponse** s'affiche avec les informations relatives à la ressource nouvellement créée.

## <a name="get-a-sas-url-with-read-write-permissions"></a>Obtenir une URL SAS avec des autorisations de lecture-écriture 

Cette section explique comment obtenir une URL SAS générée pour la ressource créée. L'URL SAS a été créée avec des autorisations de lecture-écriture et peut être utilisée pour charger des fichiers numériques dans le conteneur de ressources.

1. Sélectionnez **Ressources** -> **Répertorier les URL de ressources**.
2. Appuyez sur **Envoyer**.

    ![Charger un fichier](./media/upload-files/postman-create-sas-locator.png)

La **Réponse** s'affiche avec les informations relatives aux URL des ressources. Copiez la première URL et utilisez-la pour charger votre fichier.

## <a name="upload-a-file-to-blob-storage-using-the-upload-url"></a>Charger un fichier vers le stockage d’objets blob à l’aide de l’URL de chargement

Utilisez les API ou les kits SDK Stockage Azure (par exemple, l'[API REST de stockage](../../storage/common/storage-rest-api-auth.md), le [SDK JAVA](../../storage/blobs/storage-quickstart-blobs-java-v10.md) ou le [SDK .NET](../../storage/blobs/storage-quickstart-blobs-dotnet.md).

## <a name="next-steps"></a>Étapes suivantes

[Tutoriel : Encoder un fichier distant basé sur une URL et diffuser la vidéo en continu - REST](stream-files-tutorial-with-rest.md)
