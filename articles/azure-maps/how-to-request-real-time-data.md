---
title: Demander des données en temps réel dans Azure Maps | Microsoft Docs
description: Demandez des données en temps réel avec le service Mobility d’Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 06/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: aaab5ef4d8fc3d60a12f9e9f85f2846695fd1ab4
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/22/2019
ms.locfileid: "67329670"
---
# <a name="request-real-time-data-using-the-azure-maps-mobility-service"></a>Demander des données en temps réel avec le service Mobility d’Azure Maps

Cet article vous montre comment utiliser le [service Mobility](https://aka.ms/AzureMapsMobilityService) d’Azure Maps pour demander des données en temps réel sur les transports.

Dans cet article, vous allez apprendre à :


 * Demander les horaires d’arrivée en temps réel de toutes les lignes de bus à un arrêt donné
 * Demander des informations en temps réel sur une station de vélos donnée


## <a name="prerequisites"></a>Prérequis

Pour appeler les API de transports publics Azure Maps, vous avez besoin d’un compte et d’une clé Maps. Pour plus d’informations sur la création d’un compte et la récupération d’une clé, consultez [How to manage your Azure Maps account and keys](how-to-manage-account-keys.md) (Gérer votre compte et vos clés Azure Maps).

Cet article utilise [l’application Postman](https://www.getpostman.com/apps) pour générer des appels REST. Vous pouvez utiliser l’environnement de développement d’API que vous préférez.


## <a name="request-real-time-arrivals-for-a-stop"></a>Demander les horaires d’arrivée en temps réel à un arrêt donné

Pour demander les horaires d’arrivée en temps réel à un arrêt de transport en commun particulier, vous devez envoyer une requête à l’[API Real-time Arrivals](https://aka.ms/AzureMapsMobilityRealTimeArrivals) du [service Mobility](https://aka.ms/AzureMapsMobilityService) d’Azure Maps. Vous devez indiquer les paramètres **metroID** (numéro de la ligne) et **stopID** (numéro de l’arrêt) dans la demande. Pour en savoir plus sur l’obtention de ces paramètres, consultez notre guide pratique sur les [demandes d’itinéraires des transports en commun](https://aka.ms/AMapsHowToGuidePublicTransitRouting). 

Nous allons prendre comme exemple le numéro de ligne « 522 », qui dessert la zone « Seattle – Tacoma – Bellevue, WA », et le numéro d’arrêt « 2060603 », qui est l’arrêt de bus situé à l’adresse « Ne 24th St & 162nd Ave Ne, Bellevue WA ». Pour demander les horaires d’arrivée en temps réel des cinq prochains passages de bus à cet arrêt, effectuez les étapes suivantes :

1. Créez une collection dans laquelle stocker les demandes. Dans l’application Postman, sélectionnez **New** (Nouveau). Dans la fenêtre **Create New** (Créer nouveau), sélectionnez **Collection**. Nommez la collection puis sélectionnez le bouton **Create** (Créer).

2. Pour créer la demande, sélectionnez **New** à nouveau. Dans la fenêtre **Create New** (Créer nouveau), sélectionnez **Request** (Demande). Saisissez un **Nom de demande** pour la demande, sélectionnez la collection que vous avez créée à l’étape précédente comme emplacement dans lequel enregistrer la requête, puis sélectionnez **Save** (Enregistrer).

    ![Créer une demande dans Postman](./media/how-to-request-transit-data/postman-new.png)

3. Sélectionnez la méthode HTTP GET sous l’onglet Builder (Générateur), puis entrez l’URL suivante pour créer une requête GET.

    ```HTTP
    https://atlas.microsoft.com/mobility/realtime/arrivals/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&query=2060603&transitType=bus
    ```

4. Après une demande réussie, vous recevrez la réponse suivante.  Notez que le paramètre scheduleType définit si l’horaire d’arrivée est estimé d’après des données en temps réel ou statiques.

    ```JSON
    {
        "results": [
            {
                "arrivalMinutes": 4,
                "scheduleType": "realTime",
                "patternId": 3860436,
                "line": {
                    "lineId": 2756599,
                    "lineGroupId": 666063,
                    "direction": "forward",
                    "agencyId": 5872,
                    "agencyName": "Metro Transit",
                    "lineNumber": "226",
                    "lineDestination": "Bellevue Transit Center Crossroads",
                    "transitType": "Bus"
                },
                "stop": {
                    "stopId": 2060603,
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "position": {
                        "latitude": 47.631504,
                        "longitude": -122.125275
                    },
                    "mainTransitType": "Bus",
                    "mainAgencyId": 5872,
                    "mainAgencyName": "Metro Transit"
                }
            },
            {
                "arrivalMinutes": 30,
                "scheduleType": "scheduledTime",
                "patternId": 3860436,
                "line": {
                    "lineId": 2756599,
                    "lineGroupId": 666063,
                    "direction": "forward",
                    "agencyId": 5872,
                    "agencyName": "Metro Transit",
                    "lineNumber": "226",
                    "lineDestination": "Bellevue Transit Center Crossroads",
                    "transitType": "Bus"
                },
                "stop": {
                    "stopId": 2060603,
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "position": {
                        "latitude": 47.631504,
                        "longitude": -122.125275
                    },
                    "mainTransitType": "Bus",
                    "mainAgencyId": 5872,
                    "mainAgencyName": "Metro Transit"
                }
            }
        ]
    }
    ```


## <a name="real-time-data-for-bike-docking-station"></a>Données en temps réel relatives à une station de vélos

L’[API Get Transit Dock Info](https://aka.ms/AzureMapsMobilityTransitDock) du service Mobility d’Azure Maps permet de demander des informations statiques et en temps réel, telles que la disponibilité et les emplacements libres à une station de vélos ou de scooters donnée. Nous allons créer une requête pour obtenir des données en temps réel sur une station de vélos.

Pour envoyer une requête à l’API Get Transit Dock Info, vous avez besoin du paramètre **dockId**, qui identifie la station. Vous pouvez obtenir l’ID de la station en envoyant une requête de recherche à l’[API Get Nearby Transit](https://aka.ms/AzureMapsMobilityNearbyTransit) et en définissant le paramètre **objectType** sur « bikeDock ». Effectuez les étapes ci-dessous pour obtenir des données en temps réel sur une station de vélos.


### <a name="get-dock-id"></a>Obtenir l’ID de la station

Pour obtenir le paramètre **dockID**, envoyez une requête à l’API Get Nearby Transit en effectuant les étapes ci-dessous :

1. Dans Postman, cliquez sur **New Request** | **GET request** (Nouvelle requête > Requête GET) et nommez-la **Get dock ID** (Obtenir l’ID de la station).

2.  Sous l’onglet Builder (Générateur), sélectionnez la méthode HTTP **GET**, entrez l’URL de la requête puis cliquez sur **Send** (Envoyer).
 
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/nearby/json?subscription-key={subscription-key}&api-version=1.0&metroId=121&query=40.7663753,-73.9627498&radius=100&objectType=bikeDock
    ```

3. Après une demande réussie, vous recevrez la réponse suivante. Notez que l’**id** obtenu dans la réponse pourra être utilisé plus tard comme paramètre dans la requête envoyée à l’API Get Transit Dock Info.

    ```JSON
    {
        "results": [
            {
                "id": "121---4640799",
                "type": "bikeDock",
                "objectDetails": {
                    "availableVehicles": 0,
                    "vacantLocations": 30,
                    "lastUpdated": "2019-05-21T20:06:59-04:00",
                    "operatorInfo": {
                        "id": "80",
                        "name": "Citi Bike"
                    }
                },
                "position": {
                    "latitude": 40.767128,
                    "longitude": -73.962243
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 40.768039,
                        "longitude": -73.963413
                    },
                    "btmRightPoint": {
                        "latitude": 40.766216,
                        "longitude": -73.961072
                    }
                }
            }
        ]
    }
    ```


### <a name="get-real-time-bike-dock-status"></a>Obtenir l’état en temps réel d’une station d’accueil de vélo

À l’aide des étapes ci-dessous, envoyez une requête à l’API Get Transit Dock Info pour obtenir des données en temps réel sur la station d’accueil sélectionnée.

1. Dans Postman, cliquez sur **New Request** | **GET request** (Nouvelle requête > Requête GET) et nommez-la **Get real-time dock data** (Obtenir des données en temps réel sur une station d’accueil).

2.  Sous l’onglet Builder (Générateur), sélectionnez la méthode HTTP **GET**, entrez l’URL de la requête puis cliquez sur **Send** (Envoyer).
 
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/dock/json?subscription-key={subscription-key}&api-version=1.0&query=121---4640799
    ```

3. Après une demande réussie, vous recevrez une réponse similaire à celle-ci :

    ```JSON
    {
        "availableVehicles": 1,
        "vacantLocations": 29,
        "position": {
            "latitude": 40.767128,
            "longitude": -73.962246
        },
        "lastUpdated": "2019-05-21T20:26:47-04:00",
        "operatorInfo": {
            "id": "80",
            "name": "Citi Bike"
        }
    }
    ```


## <a name="next-steps"></a>Étapes suivantes

Découvrez comment demander des données de transit à l’aide de Mobility Service :

> [!div class="nextstepaction"]
> [Comment demander des données de transit](how-to-request-transit-data.md)

Explorer la documentation de l’API Mobility Service d’Azure Maps :

> [!div class="nextstepaction"]
> [Documentation de l’API Mobility Service](https://aka.ms/AzureMapsMobilityService)
