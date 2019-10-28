---
title: Couverture de géocodage dans Azure Maps | Microsoft Docs
description: Découvrez la couverture de géocodage dans Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 03/22/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: a5e5f4ab286289e223a2fe10ff8cf45f43309f04
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65785936"
---
# <a name="azure-maps-geocoding-coverage"></a>Couverture de géocodage Azure Maps

Lorsque vous recherchez un emplacement avec Azure Maps, le service de recherche accepte les termes de recherche et retourne les coordonnées de latitude et longitude. Ce processus est appelé « géocodage ». Toutefois, Maps n’a pas le même niveau d’information et de précision pour tous les pays et régions. Utilisez cet article pour déterminer quels types d’emplacements vous pouvez rechercher de manière fiable dans chaque région. 

La possibilité de géocoder dans un pays/une région dépend de la couverture de données de route et de la précision du service de géocodage. Les catégorisations suivantes permettent de spécifier le niveau de prise en charge du géocodage dans chaque pays/région.
* **Points d’adresse** : Les données d’adresses peuvent être résolues en coordonnées latitude/longitude dans la parcelle de l’adresse (limite de propriété). Ils sont parfois désignés comme ayant une précision « rooftop » (maximale). Il s’agit du niveau de précision le plus haut disponible pour les adresses. 
* **Numéros de résidence** : Les adresses sont interpolées en coordonnées latitude/longitude dans la rue.
* **Niveau de rue** : Les adresses sont résolues en coordonnées latitude/longitude de la rue qui contient l’adresse. Le numéro de résidence ne peut pas être traité.
* **Niveau de ville** : Les noms de ville sont pris en charge.

## <a name="americas"></a>Amérique

| Pays/région                                       | Points d’adresse | Numéros de résidence | Niveau de rue | Niveau ville | Points d’intérêt |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
| Anguilla                                            |                 |                |              |      ✓     |          ✓         |
| Antarctique                                          |                 |                |              |      ✓     |          ✓         |
| Antigua-et-Barbuda                                 |                 |                |       ✓      |      ✓     |          ✓         |
| Argentine                                           |       ✓         |        ✓       |       ✓      |      ✓     |          ✓         |
| Aruba                                               |                 |                |              |      ✓     |          ✓         |
| Les Bahamas                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Barbade                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Belize                                              |                 |                |              |      ✓     |          ✓         |
| Bermudes                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Bolivie                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Bonaire, Saint-Eustache et Saba                   |                 |                |              |      ✓     |          ✓         |
| Brésil                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Canada                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Caïmans (îles)                                      |                 |                |       ✓      |      ✓     |          ✓         |
| Chili                                               |       ✓         |        ✓       |       ✓      |      ✓     |          ✓         |
| Colombie                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Costa Rica                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Cuba                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Dominique                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Dominicana                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Équateur                                             |                 |                |       ✓      |      ✓     |          ✓         |
| El Salvador                                         |                 |                |       ✓      |      ✓     |          ✓         |
| Îles Malouines                                    |                 |                |              |      ✓     |          ✓         |
| Guyane française                                       |                 |                |       ✓      |      ✓     |          ✓         |
| Grenade                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Guadeloupe                                          |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Guam                                                |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Guatemala                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Guyane                                              |                |             |           |      ✓     |                 |
| Haïti                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Honduras                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Jamaïque                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Martinique                                          |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Mexique                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Montserrat                                          |                 |                |              |      ✓     |          ✓         |
| Nicaragua                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Panama                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Paraguay                                            |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Pérou                                                |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Porto Rico                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Saint-Barthélemy                                    |                 |                |       ✓      |      ✓     |          ✓         |
| Saint-Christophe-et-Niévès                               |                 |                |       ✓      |      ✓     |          ✓         |
| Sainte-Lucie                                         |                 |                |              |      ✓     |          ✓         |
| Saint-Martin                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Saint Pierre et Miquelon                           |                 |                |       ✓      |      ✓     |          ✓         |
| Saint-Vincent-et-les-Grenadines                    |                 |                |              |      ✓     |          ✓         |
| Saint-Martin (partie néerlandaise)                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Géorgie du Sud et îles Sandwich du Sud        |                 |                |              |      ✓     |          ✓         |
| Surinam                                            |                 |                |              |      ✓     |          ✓         |
| Trinité-et-Tobago                                 |                 |                |       ✓      |      ✓     |          ✓         |
| Îles mineures éloignées des États-Unis                |                 |                |              |      ✓     |          ✓         |
| États-Unis d’Amérique                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Uruguay                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Venezuela                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Îles Vierges britanniques                              |                 |                |              |      ✓     |          ✓         |
| Données Îles Vierges                                 |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |

## <a name="asia-pacific"></a>Asie-Pacifique

| Pays/région                                      | Points d’adresse |Numéros de résidence | Niveau de rue | Niveau ville | Points d’intérêt |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
| Samoa américaines                                      |                 |                |       ✓      |      ✓     |          ✓         |
| Australie                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Bangladesh                                          |                 |                |              |      ✓     |          ✓         |
| Bhoutan                                              |                 |                |              |      ✓     |          ✓         |
| Territoire britannique de l'océan Indien                      |                 |                |              |      ✓     |          ✓         |
| Brunéi Darussalam                                              |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Cambodge                                            |                 |                |              |      ✓     |          ✓         |
| Chine                                               |                 |                |              |      ✓     |          ✓         |
| Christmas (île)                                    |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Îles Cocos (Keeling)                             |                 |                |              |      ✓     |          ✓         |
| Comores                                             |                 |                |              |      ✓     |          ✓         |
| Cook (îles)                                        |                 |                |              |      ✓     |          ✓         |
| Fidji                                                |                  |                |              |      ✓     |          ✓        |
| Polynésie française                                    |                 |                |              |      ✓     |          ✓         |
| Heard et McDonald (îles)                   |                 |                |              |      ✓     |          ✓         |
| Hong Kong (R.A.S.)                                       |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Indonésie                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Inde                                               |        ✓        |        ✓       |       ✓      |      ✓     |                   |
| Japon                                               |                 |                |              |      ✓     |          ✓         |
| Kiribati                                            |                 |                |              |      ✓     |          ✓         |
| Corée du Sud                                         |                 |                |              |      ✓     |          ✓         |
| Laos                                                |                 |                |              |      ✓     |          ✓         |
| Macao R.A.S.                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Malaisie                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Micronésie                                          |                 |                |              |      ✓     |          ✓         |
| Mongolie                                            |                 |                |              |      ✓     |          ✓         |
| Nauru                                               |                 |                |              |      ✓     |          ✓         |
| Népal                                               |                 |                |              |      ✓     |          ✓         |
| Nouvelle-Calédonie                                       |                 |                |              |      ✓     |          ✓         |
| Nouvelle-Zélande                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Niue                                                |                 |                |              |      ✓     |          ✓         |
| Norfolk (île)                                      |                 |                |              |      ✓     |          ✓         |
| Corée du Nord                                         |                 |                |              |      ✓     |          ✓         |
| Îles Marianne du Nord                            |                 |                |       ✓      |      ✓     |          ✓         |
| Pakistan                                            |                 |                |              |      ✓     |          ✓         |
| Palau                                               |                 |                |              |      ✓     |          ✓         |
| Papouasie Nouvelle Guinée                                    |                 |                |              |      ✓     |          ✓         |
| Îles Paracels                                     |                 |                |              |      ✓     |                    |
| Philippines                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Île Pitcairn                                            |                 |                |              |      ✓     |          ✓         |
| Samoa                                               |                 |                |              |      ✓     |          ✓         |
| Îles Senkaku/Diaoyutai                                     |        ✓        |                |              |      ✓     |          ✓         |
| Singapour                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Salomon (îles)                                     |                 |                |              |      ✓     |          ✓         |
| Îles Kouriles                                     |        ✓        |                |              |      ✓     |          ✓         |
| Îles Spratleys                                     |                 |                |              |      ✓     |                    |
| Sri Lanka                                           |                 |                |              |      ✓     |          ✓         |
| Taïwan                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Thaïlande                                            |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Tokelau                                             |                 |                |              |      ✓     |          ✓         |
| Tonga                                               |                 |                |              |      ✓     |          ✓         |
| Turques-et-Caïques (îles)                            |                 |                |              |      ✓     |          ✓         |
| Tuvalu                                              |                 |                |              |      ✓     |          ✓         |
| Vanuatu                                             |                 |                |              |      ✓     |          ✓         |
| Vietnam                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Wallis et Futuna                                   |                 |                |              |      ✓     |          ✓         |

## <a name="europe"></a>Europe

| Pays/région                                      | Points d’adresse |Numéros de résidence | Niveau de rue | Niveau ville | Points d’intérêt |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
| Albanie                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Andorre                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Arménie                                             |        ✓        |        ✓       |              |      ✓     |          ✓         |
| Autriche                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Azerbaïdjan                                          |        ✓        |        ✓       |              |      ✓     |          ✓         |
| Belgique                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Bosnie-Herzégovine                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Bulgarie                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Bélarus                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Croatie                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Chypre                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| République tchèque                                      |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Danemark                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Estonie                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Féroé (îles)                                       |                 |                |              |      ✓     |          ✓         |
| Finlande                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| France                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Géorgie                                             |        ✓        |        ✓       |              |      ✓     |          ✓         |
| Allemagne                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Gibraltar                                           |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Grèce                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Groenland                                           |                 |                |              |      ✓     |          ✓         |
| Guernesey                                            |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Hongrie                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Islande                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Irlande                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Île de Man                                         |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Italie                                               |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Jan Mayen                                           |        ✓        |                |              |      ✓     |          ✓         |
| Jersey                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Kazakhstan                                          |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Kosovo                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Kirghizistan                                          |                 |                |              |      ✓     |          ✓         |
| Lettonie                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Liechtenstein                                       |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Lituanie                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Luxembourg                                          |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Macédoine du Nord                                     |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Malte                                               |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Moldova                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Monaco                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Monténégro                                          |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Pays-bas                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Norvège                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Pologne                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Portugal                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| +Açores et Madère                                 |                 |                |       ✓      |      ✓     |          ✓         |
| Roumanie                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Fédération de Russie                                  |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Saint-Marin                                          |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Serbie                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Slovaquie                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Slovénie                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Espagne                                               |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Svalbard                                            |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Suède                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Suisse                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Tadjikistan                                          |                 |                |              |      ✓     |          ✓         |
| Turquie                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Turkménistan                                        |                 |                |              |      ✓     |          ✓         |
| Ukraine                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Royaume-Uni                                      |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Ouzbékistan                                          |                 |                |              |      ✓     |          ✓         |
| État de la Cité du Vatican                                        |                 |                |       ✓      |      ✓     |          ✓         |


## <a name="middle-east-and-africa"></a>Moyen-Orient et Afrique

| Pays/région                                      | Points d’adresse |Numéros de résidence | Niveau de rue | Niveau ville | Points d’intérêt |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
| Afghanistan                                         |                 |                |              |      ✓     |          ✓         |
| Algérie                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Angola                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Bahreïn                                             |        ✓        |       ✓        |       ✓      |      ✓     |          ✓         |
| Bénin                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Botswana                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Bouvet (île)                                       |                 |                |              |      ✓     |          ✓         |
| Burkina Faso                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Burundi                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Cameroun                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Cabo Verde                                          |                 |                |       ✓      |      ✓     |          ✓         |
| République centrafricaine                            |                 |                |       ✓      |      ✓     |          ✓         |
| Tchad                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Congo                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Côte d’Ivoire                                       |                 |                |       ✓      |      ✓     |          ✓         |
| République démocratique du Congo                    |                 |                |       ✓      |      ✓     |          ✓         |
| Djibouti                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Égypte                                               |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Guinée équatoriale, République de                      |                 |                |       ✓      |      ✓     |          ✓         |
| Érythrée                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Éthiopie                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Terres australes françaises|                        |                |              |      ✓     |          ✓         |
| Gabon                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Gambie                                              |                 |                |              |      ✓     |          ✓         |
| Ghana                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Guinée                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Guinée-Bissau                                       |                 |                |       ✓      |      ✓     |          ✓         |
| Iran                                                |                 |                |              |      ✓     |          ✓         |
| Irak                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Israël                                              |        ✓        |       ✓        |              |      ✓     |          ✓         |
| Jordanie                                              |        ✓        |       ✓        |       ✓      |      ✓     |          ✓         |
| Kenya                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Koweït                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Liban                                             |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Lesotho                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Liberia                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Libye                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Madagascar                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Malawi                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Maldives                                            |                 |                |              |      ✓     |          ✓         |
| Mali                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Marshall (îles)                                    |                 |                |              |      ✓     |          ✓         |
| Mauritanie                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Maurice (île)                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Mayotte                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Maroc                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Mozambique                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Myanmar                                             |                 |                |              |      ✓     |          ✓         |
| Namibie                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Niger                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Nigeria                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Oman                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Qatar                                               |        ✓        |                |       ✓      |      ✓     |          ✓         |
| La Réunion                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Rwanda                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Sainte-Hélène                                        |                 |                |              |      ✓     |          ✓         |
| Arabie Saoudite                                        |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Sénégal                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Seychelles                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Sierra Leone                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Somalie                                             |                 |                |              |      ✓     |          ✓         |
| Afrique du Sud                                        |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Soudan du Sud                                         |                 |                |       ✓      |      ✓     |          ✓         |
| Soudan                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Swaziland                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Syrie                                               |                 |                |              |      ✓     |          ✓         |
| São Tomé et Príncipe                               |                 |                |       ✓      |      ✓     |          ✓         |
| Tanzanie                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Togo                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Tunisie                                             |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Ouganda                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Émirats Arabes Unis                                |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Yémen                                               |                 |                |              |      ✓     |          ✓         |
| Zambie                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Zimbabwe                                            |                 |                |       ✓      |      ✓     |          ✓         |



## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le géocodage Azure Maps, consultez les pages de référence sur la [recherche](https://docs.microsoft.com/rest/api/maps/search).

Découvrez les [zones de couverture pour les services de trafic Maps](traffic-coverage.md). 

