---
title: Ajouter un certificat d’autorité de certification personnalisé - Gestion des API Azure | Microsoft Docs
description: Découvrez comment ajouter un certificat d’autorité de certification personnalisé dans la Gestion des API Azure.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/20/2018
ms.author: apimpm
ms.openlocfilehash: 21d5869f2bcdfb6383b6ef89869d8098135ea7ee
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70073606"
---
# <a name="how-to-add-a-custom-ca-certificate-in-azure-api-management"></a>Guide pratique pour ajouter un certificat d’autorité de certification personnalisé dans la Gestion des API Azure

La Gestion des API Azure permet d’installer des certificats d’autorité de certification dans les magasins de certificats racines et intermédiaires approuvés, présents sur l’ordinateur. Vous devez utiliser cette fonctionnalité si vos services nécessitent un certificat d’autorité de certification personnalisé.

Cet article montre comment gérer les certificats d’autorité de certification de l’instance de service Gestion des API Azure dans le portail Azure.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="step1"> </a>Charger un certificat d’autorité de certification

![Ajouter des certificats d’autorité de certification](media/api-management-howto-ca-certificates/00.png)

Suivez les étapes ci-dessous pour charger un nouveau certificat d’autorité de certification. Si vous n’avez pas encore créé d’instance de service Gestion des API, consultez le tutoriel [Créer une instance du service Gestion des API Azure](get-started-create-service-instance.md).

1. Accédez à votre instance de service Gestion des API Azure dans le Portail Azure.

2. Sélectionnez **Certificats d’autorité de certification** dans le menu.

3. Cliquez sur le bouton **+ Ajouter** .  

    ![Ajouter des certificats d’autorité de certification](media/api-management-howto-ca-certificates/01.png)  

4. Recherchez le certificat, puis choisissez un magasin de certificats. Seule la clé publique doit être fournie. Le mot de passe n’est pas nécessaire.

    ![Ajouter des certificats d’autorité de certification](media/api-management-howto-ca-certificates/02.png)  

5. Cliquez sur **Enregistrer**. Cette opération peut prendre quelques minutes.

    ![Ajouter des certificats d’autorité de certification](media/api-management-howto-ca-certificates/03.png)  

> [!NOTE]
> Vous pouvez charger un certificat d’autorité de certification à l’aide de la commande Powershell `New-AzApiManagementSystemCertificate`.

## <a name="step1a"></a>Suppression d’un certificat client

Pour supprimer un certificat, cliquez sur le menu contextuel **...** , puis sélectionnez **Supprimer** à côté du certificat.

![Supprimer des certificats d’autorité de certification](media/api-management-howto-ca-certificates/04.png)  

[Upload a CA certificate]: #step1
[Delete a CA certificate]: #step1a
