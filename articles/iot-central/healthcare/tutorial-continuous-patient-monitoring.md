---
title: Créer une application de surveillance continue des patients avec Azure IoT Central | Microsoft Docs
description: Découvrez comment créer une application de surveillance continue des patients à l’aide de modèles d’application Azure IoT Central.
author: philmea
ms.author: philmea
ms.date: 09/24/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 97a215d8f111753c8fcc857fe4c48956c1236b3b
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027442"
---
# <a name="tutorial-deploy-and-walkthrough-a-continuous-patient-monitoring-app-template"></a>Didacticiel : Déployer et parcourir pas à pas un modèle d’application de surveillance continue des patients

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Ce tutoriel vous montre comment vous lancer, en votre qualité de générateur de solutions, en déployant un modèle d’application de surveillance continue des patients IoT Central. Vous allez apprendre à déployer le modèle, découvrir les fonctionnalités prêtes à l’emploi qui vous sont proposées et les différentes possibilités qui s’offrent à vous.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un modèle d’application
> * Parcourir pas à pas le modèle d’application

## <a name="create-an-application-template"></a>Créer un modèle d’application

Accédez au [site web du gestionnaire d’applications Azure IoT Central](https://apps.azureiotcentral.com/). Sélectionnez **Build** (Générer) dans la barre de navigation de gauche, puis cliquez sur l’onglet **Healthcare** (Santé). 

>[!div class="mx-imgBorder"] 
>![Gestionnaire d’applications – Healthcare](media/app-manager-health.png)

Cliquez sur le bouton **Create app** (Créer une application) pour commencer à créer votre application, puis connectez-vous avec un compte Microsoft personnel, professionnel ou scolaire. Cela vous amène à la page **New application** (Nouvelle application).

>[!div class="mx-imgBorder"] 
>![Créer une application de santé](media/app-manager-health-create.png)

Pour créer votre application :

1. Azure IoT Central suggère automatiquement un nom d’application basé sur le modèle que vous avez sélectionné. Vous pouvez accepter ce nom ou entrer le nom d’application convivial de votre choix, par exemple **Surveillance continue des patients**. Azure IoT Central génère aussi un préfixe d’URL unique basé sur le nom de l’application. Si vous le souhaitez, vous pouvez remplacer ce préfixe d’URL par une chaîne plus facile à mémoriser.

2. Vous pouvez choisir de créer une application en essai gratuit (**Trial**) ou une application avec paiement à l’utilisation (**Pay-As-You-Go**). Les applications **Trial** sont gratuites pendant sept jours avant expiration et autorisent une utilisation sur cinq appareils gratuits. Elles peuvent être passées en paiement à l’utilisation à tout moment avant leur expiration. Si vous créez une application en essai gratuit (Trial), entrez vos coordonnées et indiquez si vous souhaitez recevoir des informations et des conseils de la part de Microsoft. Les applications avec paiement à l’utilisation (**Pay-As-You-Go**) prennent en charge jusqu’à deux appareils gratuits et nécessitent vos informations d’abonnement Azure.

3. Sélectionnez **Create** (Créer) au bas de la page pour déployer votre application.

## <a name="walk-through-the-application-template"></a>Parcourir pas à pas le modèle d’application

### <a name="dashboards"></a>Tableaux de bord

Après avoir déployé le modèle d’application, vous accédez dans un premier temps au **tableau de bord de surveillance des patients Lamna**. Lamna Healthcare est un ensemble hospitalier fictif qui se compose de deux hôpitaux : Woodgrove Hospital et Burkville Hospital. Dans ce tableau de bord de commande du Woodgrove Hospital figurent des informations et autres données de télémétrie sur les appareils de ce modèle ainsi qu’un ensemble de commandes, de travaux et d’actions que vous pouvez effectuer. À partir de ce tableau, vous pouvez :

* Consulter les données de télémétrie et les propriétés des appareils, comme le **niveau de charge de la batterie** ou le statut de **connectivité**.

* Affichez le **plan d’étage** et l’emplacement de l’appareil Smart Vitals Patch.

* **Reprovisionner** le Smart Vitals Patch pour un nouveau patient.

* Voir un exemple de **tableau de bord de fournisseur** dont pourrait se servir une équipe de soins hospitaliers pour assurer le suivi de leurs patients.

* Changer le **statut de surveillance** de votre appareil pour indiquer s’il est utilisé à l’hôpital ou dans un scénario à distance.

>[!div class="mx-imgBorder"] 
>![Surveillance Lamna à l’hôpital](media/lamna-in-patient.png)

Vous pouvez aussi cliquer sur **Go to remote patient dashboard** (Accéder au tableau de bord de surveillance à distance des patients) pour voir le deuxième tableau de bord de commande utilisé pour le Burkville Hospital. Ce tableau de bord contient un ensemble similaire d’actions, de données de télémétrie et d’informations. Par ailleurs, vous pouvez voir les différents appareils utilisés et **mettre à jour leur microprogramme**.

>[!div class="mx-imgBorder"] 
>![Lamna à distance](media/lamna-remote.png)

Les deux tableaux de bord proposent des liens pour revenir à cette documentation.

### <a name="device-templates"></a>Modèles d’appareil

Si vous cliquez sur l’onglet **Device templates** (Modèles d’appareils), vous constaterez qu’il existe deux types d’appareil dans le modèle :

* **Smart Vitals Patch** : cet appareil se présente sous la forme d’un patch qui mesure divers signes vitaux susceptibles de servir à la surveillance des patients dans et en dehors de l’hôpital. Si vous cliquez sur le modèle, vous constaterez qu’en plus d’envoyer des données sur l’appareil comme le niveau de charge de la batterie et la température de l’appareil, le patch envoie aussi des données sur la santé des patients comme la fréquence respiratoire et la pression artérielle.

* **Smart Knee Brace** : cet appareil se présente sous la forme d’une attelle de genou que les patients peuvent utiliser à la suite d’une opération de reconstruction du genou. Si vous cliquez sur ce modèle, outre les données de l’appareil, vous trouverez des informations sur certaines aptitudes, comme l’amplitude de mouvement et la mobilité.

>[!div class="mx-imgBorder"] 
>![Modèle d’appareil Smart Vitals Patch](media/smart-vitals-device-template.png)

Si vous cliquez sur l’onglet **Device groups** (Groupes d’appareils), vous pourrez aussi constater que des groupes d’appareils sont créés automatiquement pour ces modèles d’appareils.

### <a name="rules"></a>Règles

En accédant à l’onglet de règles, vous constaterez la présence de trois règles qui existent dans le modèle d’application :

* **Brace temperature high** (Température élevée de l’attelle) : cette règle se déclenche quand la température de l’attelle de genou Smart Knee Brace est supérieure à 95 &deg;F (35 °C) sur une période de 5 minutes. Vous pouvez utiliser cette règle pour alerter le patient et l’équipe de soins pour qu’elle refroidisse l’appareil à distance.

* **Fall detected** (Chute détectée) : cette règle se déclenche si une chute du patient est détectée. Vous pouvez utiliser cette règle pour configurer une action d’intervention d’équipe opérationnelle pour secourir le patient qui a chuté.

* **Patch battery low** (Faible niveau de charge du patch) : cette règle se déclenche quand le niveau charge de la batterie de l’appareil est inférieur à 10 %. Vous pouvez utiliser cette règle pour déclencher une notification à l’intention du patient pour qu’il charge son appareil.

>[!div class="mx-imgBorder"] 
>![Règle Brace temperature high rule (Température élevée de l’attelle)](media/brace-temp-rule.png)

### <a name="devices"></a>Appareils

Cliquez sur l’onglet **Devices** (Appareils), puis sélectionnez une instance de **Smart Knee Brace**. Vous constatez alors qu’il existe trois vues qui permettent d’explorer les informations sur l’appareil que vous avez sélectionné. Ces vues sont créées et publiées au moment de générer le modèle de l’appareil. Cela signifie qu’elles sont identiques sur tous les appareils que vous connectez ou simulez.

La vue **Dashboard** (Tableau de bord) offre une vue d’ensemble des données de télémétrie et des propriétés de l’appareil destinées aux opérateurs.

L’onglet **Properties** (Propriétés) vous permet de modifier les propriétés cloud et de lire/écrire les propriétés de l’appareil.

L’onglet **Commands** (Commandes) vous permet d’exécuter des commandes qui ont été modélisées dans votre modèle d’appareil.

>[!div class="mx-imgBorder"] 
>![Vues de l’attelle de genou Smart Knee Brace](media/knee-brace-dashboard.png)

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous n’envisagez pas de continuer à utiliser cette application, supprimez-la en accédant à **Administration > Application settings** (Administration > Paramètre d’application), puis cliquez sur **Delete** (Supprimer).

>[!div class="mx-imgBorder"] 
>![Supprimer l’application](media/admin-delete.png)

## <a name="next-steps"></a>Étapes suivantes

Passez à l’article suivant pour apprendre à créer un tableau de bord de fournisseur qui se connecte à votre application IoT Central.

> [!div class="nextstepaction"]
> [Créer un tableau de bord de fournisseur](howto-health-data-triage.md)