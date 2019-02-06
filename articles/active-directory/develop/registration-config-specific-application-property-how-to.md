---
title: Guide pratique pour remplir les champs spécifiques d’une application personnalisée | Microsoft Docs
description: Conseils sur la manière de remplir des champs spécifiques lorsque vous inscrivez une application personnalisée auprès d’Azure AD
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: celested
ms.openlocfilehash: 90b7638acf6df721a16bdcca6ceebb6f9d65ca2c
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/28/2019
ms.locfileid: "55096821"
---
# <a name="how-to-fill-out-specific-fields-for-a-custom-developed-application"></a>Guide pratique pour remplir les champs spécifiques d’une application personnalisée

Cet article décrit brièvement tous les champs disponibles dans le formulaire d’inscription d’application du [portail Azure](https://portal.azure.com).

## <a name="register-a-new-application"></a>Inscrire une nouvelle application

-   Pour inscrire une nouvelle application, accédez au [portail Azure](https://portal.azure.com).

-   Dans le volet de navigation gauche, cliquez sur **Azure Active Directory.**

-   Choisissez **Inscriptions des applications**, puis cliquez sur **Ajouter**.

-   Le formulaire d’inscription d’application s’affiche ensuite.

## <a name="fields-in-the-application-registration-form"></a>Champs du formulaire d’inscription d’application


| Champ            | Description                                                                              |
|------------------|------------------------------------------------------------------------------------------|
| NOM             | Le nom de l’application. Il doit être composé de quatre caractères minimum.                |
| Type d’application | **Application web/API web** : une application qui représente une application web, une API web, ou les deux 
| |**Native** : une application qui peut être installée sur l’appareil ou l’ordinateur d’un utilisateur           |
| URL de connexion      | L’URL à laquelle les utilisateurs peuvent se connecter pour utiliser votre application.                                  |

Après avoir rempli les champs ci-dessus, l’application est inscrite dans le portail Azure et vous êtes redirigé vers la page de l’application. Le bouton **Paramètres** du volet de l’application ouvre la page Paramètres, qui comporte des champs supplémentaires vous permettant de personnaliser votre application. Le tableau ci-dessous décrit tous les champs de la page Paramètres. Notez que vous voyez uniquement un sous-ensemble de ces champs, selon que vous avez créez une application web ou une application native.

| Champ           | Description                                                                                                                                                                                                                                                                                                     |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ID de l'application  | Lorsque vous inscrivez une application, Azure AD attribue un ID d’application à votre application. Cet ID d’application peut être utilisé pour identifier de façon unique votre application dans les demandes d’authentification à Azure AD, ainsi que pour accéder à des ressources telles que l’API Graph.                                                          |
| URI ID d’application      | Cette valeur doit être un URI unique, généralement au format **https://&lt;nom\_locataire&gt;/&lt;nom\_application&gt;.** Cet élément est utilisé dans le flux d’octroi d’autorisation en tant qu’identificateur unique pour spécifier la ressource pour laquelle le jeton doit être émis. Il devient également la revendication « aud » dans le jeton d’accès émis. |
| Charger un nouveau logo | Ce champ vous permet de charger un logo pour votre application. Le logo doit être au format .bmp, .jpg ou .png, et la taille du fichier doit être inférieure à 100 ko. Les dimensions de l’image doivent être de 215 x 215 pixels et les dimensions de l’image centrale de 94 x 94 pixels.                                                       |
| URL de la page d’accueil   | Il s’agit de l’URL de connexion spécifiée lors de l’inscription de l’application.                                                                                                                                                                                                                                              |
| URL de déconnexion      | Il s’agit de l’URL de déconnexion unique. Azure AD envoie une demande de déconnexion à cette URL lorsque l’utilisateur efface sa session avec Azure AD à l’aide d’une autre application inscrite.                                                                                                                                       |
| Mutualisé  | Ce paramètre spécifie si l’application peut être utilisée par plusieurs locataires. En règle générale, cela signifie que des organisations externes peuvent utiliser votre application en l’inscrivant dans leur locataire et en accordant l’accès aux données de leur organisation.                                                                   |
| URL de réponse      | Les URL de réponse sont les points de terminaison auxquels Azure AD renvoie les jetons demandés par votre application.                                                                                                                                                                                                          |
| URI de redirection   | Pour les applications natives, c’est l’emplacement vers lequel l’utilisateur est dirigé après avoir été authentifié. Azure AD vérifie que l’URI de redirection que votre application fournit dans la demande OAuth 2.0 correspond à l’une des valeurs inscrites dans le portail.                                                            |
| Clés            | Vous pouvez créer des clés pour accéder par programme aux API web sécurisées par Azure AD sans aucune interaction de l’utilisateur. Depuis la page \*\*Clés\*\*, saisissez une description de clé et la date d’expiration,puis enregistrez pour générer la clé. Veillez à l’enregistrer dans un endroit sûr, car vous ne pourrez pas y accéder ultérieurement.             |

## <a name="next-steps"></a>Étapes suivantes
[Gestion des applications avec Azure Active Directory](../manage-apps/what-is-application-management.md)
