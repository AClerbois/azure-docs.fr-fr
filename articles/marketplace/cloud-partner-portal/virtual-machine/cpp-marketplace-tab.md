---
title: Onglet Place de marché de machine virtuelle dans le portail Microsoft Cloud Partner pour Azure | Microsoft Docs
description: Décrit l’onglet Place de marché utilisé pour créer une offre de machine virtuelle de la Place de marché Microsoft Azure.
services: Azure, Marketplace, Cloud Partner Portal, virtual machine
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/19/2018
ms.author: pbutlerm
ms.openlocfilehash: b68f7004366dcb502ffc8332cf8266a434c7ecfb
ms.sourcegitcommit: 8314421d78cd83b2e7d86f128bde94857134d8e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/19/2018
ms.locfileid: "51977146"
---
# <a name="virtual-machine-marketplace-tab"></a>Onglet Place de marché de machine virtuelle

L’onglet **Place de marché** de la page **Nouvelle offre** vous permet de proposer à vos clients potentiels des accords et informations juridiques, commerciaux et marketing, ainsi que de gérer des prospects depuis la Place de marché. Ce long formulaire est divisé en quatre sections : **Vue d’ensemble**, **Artefacts marketing**, **Gestion des prospects** et **Informations légales**. 

## <a name="overview-section"></a>Section Vue d’ensemble
Dans cette section, vous entrez les informations générales sur votre offre de la Place de marché Microsoft Azure.  Un astérisque (*) en regard du nom du champ indique que ce champ est obligatoire.

![Section Vue d’ensemble de l’onglet Place de marché sur le formulaire Nouvelle offre des machines virtuelles](./media/publishvm_008.png)

Le tableau suivant décrit l’objectif et le contenu de ces champs.

|  **Champ**                |     **Description**                                                          |
|  ---------                |     ---------------                                                          |
| **Titre**                 | Titre de l’offre. Il s’agit souvent du nom formel long. Il est affiché de façon visible dans la Place de marché.  Longueur maximale de 50 caractères. |
| **Résumé**               | Brève description de la finalité ou de la fonction de la solution.  Longueur maximale de 100 caractères. |
| **Long Summary** (Résumé long)          | Description de la finalité ou de la fonction de la solution.  Longueur maximale de 256 caractères. |
| **Description**           | Description de la solution.  Longueur maximale de 3 000 caractères ; prend en charge la mise en forme HTML simple. |
| **Marketing Identifier** (Identificateur marketing)  | URL unique à associer à cette offre. Elle inclut généralement le nom de votre solution et de votre organisation, avec une longueur maximale de 50 caractères.  Par exemple :  <br/> `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sampleApp`  |
| **ID d’abonnement pour préversion** | Ajoutez entre 1 et 100 identificateurs d’abonnement d’utilisateurs de la préversion. Ces abonnements autorisés ont accès à l’offre dès sa publication, avant sa mise en service. |
| **Liens utiles**          | Ajoutez des URL vers des pages de documentation, des notes de version, des FAQ, etc. |
| **Catégories suggérées (cinq au maximum)** | Sélection multiple de catégories commerciales et techniques auxquelles l’offre s’associe le mieux.  Cinq catégories maximales autorisées.  |
|  |  |


## <a name="marketing-artifacts-section"></a>Section Artefacts marketing

Cette deuxième section est divisée en trois sous-sections : **Logos**, **Capture d’écran** et **Vidéos**. Les logos sont les seuls artefacts marketing obligatoires ; néanmoins, nous vous recommandons d’ajouter les autres artefacts pour mieux attirer les clients.

![Section Artefacts Marketing de l’onglet Place de marché sur le formulaire Nouvelle offre pour les machines virtuelles](./media/publishvm_009.png)

|  **Champ**                |     **Description**                                                          |
|  ---------                |     ---------------                                                          |
| *Logos*  |  |
| **Petite**                 | Image bitmap .ico de 40x40 pixels                                                      |
| **Moyenne**                | Image bitmap .ico de 90x90 pixels                                                      |
| **Grande**                 | Image bitmap .ico de 115x115 pixels                                                   |
| **Large**                  | Image bitmap .ico de 255x115 pixels                                                    |
| **Bannière**                  | Image bitmap de 815x290.  Facultatif. En revanche, une fois l’icône de bannière chargée, il n’est plus possible de la supprimer. |
| *Captures d’écran*  | Facultatif ; cinq captures d’écran maximum par référence SKU. |
| **Nom**                  | Nom ou titre <!-- TODO - max char length? none specified in UI -->                               |
| **Image**                 | Image de capture d’écran, 533x324 pixels                                         |
| *Vidéos*  |  |
| **Nom**                  | Nom ou titre <!-- TODO - max char length? -->                              |
| **Lien**                  | URL de la vidéo, hébergée sur YouTube ou Vimeo                                        |
| **Vidéo miniature**             | Image bitmap de 533x324 pixels                                                               |
|  |  |


### <a name="logo-guidelines"></a>Instructions concernant le logo

<!-- TD: It seems like this section could be better located in some common area, maybe a AMP Marketing/Design section 
+1 this should all be in a common area and referenced from here to that location.-->

Tous les logos chargés sur le Portail Cloud Partner doivent respecter ces instructions :

*  Le design Azure a une palette de couleurs simple. Limitez le nombre de couleurs primaires et secondaires sur votre logo.
*  Les couleurs de thème du portail Azure sont le blanc et le noir. Évitez donc d’utiliser ces couleurs comme couleur d’arrière-plan pour vos logos. Utilisez des couleurs qui mettent vos logos en évidence dans le portail Azure. Nous vous recommandons d’utiliser des couleurs primaires simples. Si vous utilisez un arrière-plan transparent, assurez-vous que le logo/texte n’est ni blanc ni noir ni bleu.
*  N’utilisez pas d’arrière-plan dégradé sur votre logo.
*  Évitez de placer du texte, même s’il s’agit du nom de votre société ou de votre marque, sur le logo. L’apparence de votre logo doit être « plate » et éviter les dégradés.
*  N’étirez pas le logo.

#### <a name="hero-logo"></a>Bannière

Facultatif. En revanche, une fois l’icône de bannière chargée, il n’est plus possible de la supprimer.  L’icône de bannière doit respecter les recommandations suivantes :

*  Les arrière-plans noirs, blancs et transparents ne sont pas autorisés pour les icônes de bannière.
*  Évitez d’utiliser une couleur claire dans l’arrière-plan de l’icône de bannière.  Le nom d’affichage de l’éditeur, le titre du plan et le résumé long de l’offre s’affichent en couleur de police blanche et doivent ressortir par rapport à l’arrière-plan.
*  Évitez d’utiliser la majorité du texte lorsque vous concevez la bannière.  Le nom de l’éditeur, le titre du plan, le résumé long de l’offre et un bouton Créer sont incorporés par programmation à l’intérieur de l’icône de bannière quand l’offre est répertoriée. 
* Insérez un rectangle inutilisé sur le côté droit de votre icône de bannière, d’une taille de 415x100 pixels, et décalez-le de 370 pixels à partir de la gauche.  

À titre d’exemple, l’icône de bannière suivante concerne Azure Container Service.  <!-- TD: It would be nice to have the raw bitmap, e.g.before and after embedding. -->

![Exemple d’icône de bannière pour Azure Container Service](./media/publishvm_010.png)


### <a name="marketing-information-example"></a>Exemple d’informations marketing 

L’image suivante montre comment les informations marketing sont affichées dans la page principale du produit de Microsoft Windows Server.

![Exemple de page de produit pour Microsoft Windows Server](./media/publishvm_011.png)


## <a name="lead-management-section"></a>Section Gestion des prospects
<!-- this all should be referenced in a common location for lead management, not in this file. nothing unique for a vm specifically. -->

La troisième section vous permet de collecter les prospects générés à partir de vos offres de la Place de marché Azure. Elle contient les options de stockage suivantes (dans une liste déroulante) correspondant aux informations sur les prospects.

* **Aucun** : valeur par défaut. Les informations relatives aux prospects ne sont pas collectées.
* Table Azure : les informations sont écrites dans la table Azure spécifiée par une chaîne de connexion.
* Dynamics CRM Online : les informations sont écrites dans l’instance [Microsoft Dynamics 365 Online](https://dynamics.microsoft.com/) spécifiée par une URL et des informations d’identification d’authentification.
* Point de terminaison HTTPS : les informations sont écrites dans le point de terminaison HTTPS spécifié en tant que charge utile JSON.
* Marketo : les informations sont écrites dans l’instance [Marketo](https://www.marketo.com/) spécifiée par l’ID du serveur, l’ID munchkin et l’ID de formulaire.
* Salesforce : les informations sont écrites dans une base de données [Salesforce](https://www.salesforce.com/), spécifiée par un identificateur d’objet.

Après que vous avez publié votre offre, la connexion de prospect est vérifiée et un prospect test est automatiquement envoyé vers la destination configurée. Les informations relatives aux prospects doivent être gérées en continu, et ces paramètres doivent être immédiatement mis à jour quand des modifications sont apportées à votre architecture de gestion des clients.

<!-- TD: For more info, see [Need a topic on lead information and processing that mimics the Appendix of the VM Pub Guide]. -->

## <a name="legal-section"></a>Section Informations légales

Cette dernière section vous permet de fournir les deux documents juridiques nécessaires pour chaque offre : la politique de confidentialité et les conditions d’utilisation.

|  **Champ**                |     **Description**                                                          |
|  ---------                |     ---------------                                                          |
| **URL de la politique de confidentialité**    | URL de votre politique de confidentialité publiée                                            |
| **Conditions d’utilisation**          | Stratégie sous forme de texte brut ou au format HTML simple  <!-- TODO - max char length? -->       |
|  |  |

<br/>

Dans l’onglet [Support](./cpp-support-tab.md) suivant, vous allez indiquer les ressources de support utilisateur et techniques associées à votre offre.

