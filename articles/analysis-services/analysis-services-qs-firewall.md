---
title: 'Démarrage rapide : configurer un pare-feu pour un serveur Analysis Services dans Azure | Microsoft Docs'
description: Découvrez comment configurer un pare-feu pour une instance de serveur Analysis Services dans Azure.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: quickstart
ms.date: 07/02/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 52ccaa17b9994751bb9dfd54848e20aef90146c3
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69905876"
---
# <a name="quickstart-configure-server-firewall---portal"></a>Démarrage rapide : Configurer un pare-feu de serveur - Portail

Ce démarrage rapide vous aide à configurer un pare-feu pour votre serveur Azure Analysis Services. L’activation d’un pare-feu et la configuration de plages d’adresses PI pour les ordinateurs accédant à votre serveur uniquement constituent une partie importante de la protection de votre serveur et de vos données.

## <a name="prerequisites"></a>Prérequis

- Un serveur Analysis Services dans votre abonnement. Pour en savoir plus, consultez [Démarrage rapide : Créer un serveur - Portail](analysis-services-create-server.md) ou [Démarrage rapide : Créer un serveur - PowerShell](analysis-services-create-powershell.md)
- Une ou plusieurs plages d’adresses IP pour les ordinateurs clients (si nécessaire).
- Notez que le scénario d’importation à partir de Power BI Premium n’est pas pris en charge actuellement.

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure. 

[Se connecter au portail](https://portal.azure.com)

## <a name="configure-a-firewall"></a>Configurer un pare-feu

1. Cliquez sur votre serveur pour ouvrir la page Vue d’ensemble. 
2. Dans **Paramètres** > **Pare-feu** > **Activer le pare-feu**, cliquez sur **Activé**.
3. Pour autoriser l’accès DirectQuery à partir du service Power BI, dans **Allow access from Power BI** (Autoriser l’accès à partir de Power BI), cliquez sur **Activé**.  
4. (Facultatif) Spécifiez une ou plusieurs plages d’adresses IP. Entrez un nom et une adresse IP de début et de fin pour chaque plage. Le nom de la règle de pare-feu doit être limité à 128 caractères et peut uniquement contenir des caractères majuscules, des minuscules, des chiffres, le trait de soulignement et le trait d’union. Les espaces et caractères spéciaux ne sont pas autorisés.
5. Cliquez sur **Enregistrer**.

     ![Paramètres du pare-feu](./media/analysis-services-qs-firewall/aas-qs-firewall.png)

## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsque vous n’en avez plus besoin, supprimez les plages d’adresses IP ou désactivez le pare-feu.

## <a name="next-steps"></a>Étapes suivantes
Dans ce démarrage rapide, vous avez appris comment configurer un pare-feu pour votre serveur. Maintenant que vous disposez d’un serveur et que vous l’avez protégé avec un pare-feu, vous pouvez ajouter un exemple de modèle de données de base à celui-ci à partir du portail. Un exemple de modèle permet d’en savoir plus sur la configuration des rôles de base de données de modèle et le test des connexions client. Pour en savoir plus, passez au tutoriel sur l’ajout d’un exemple de modèle.

> [!div class="nextstepaction"]
> [Tutoriel : Ajouter un exemple de modèle à votre serveur](analysis-services-create-sample-model.md)
