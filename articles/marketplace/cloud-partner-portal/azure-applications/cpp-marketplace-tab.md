---
title: Onglet de la place de marché offre d’application Azure
description: Utilisez l’onglet Place de marché pour identifier les ressources marketing pour une offre d’application Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pabutler
ms.openlocfilehash: 7ea6e6be0597a114b02fad8c41e37d21ce1f6028
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64942968"
---
# <a name="azure-application-marketplace-tab"></a>Onglet Place de marché - Application Azure

Utilisez l’onglet Place de marché pour décrire votre application Azure et fournir des ressources marketing. Cet onglet comprend les formulaires suivants : Overview (Vue d’ensemble), Marketing Artifacts (Artefacts marketing), Lead Management (Gestion des prospects) et Legal (Informations juridiques).

## <a name="overview-form"></a>Formulaire Vue d'ensemble

Le formulaire Vue d’ensemble contient les champs obligatoires et facultatifs illustrés dans la capture d’écran suivante. Les champs obligatoires sont indiqués par un astérisque (*).

![Formulaire Vue d'ensemble](./media/azureapp-marketplace-overview.png)

Le tableau suivant décrit les paramètres à utiliser pour la création d’une vitrine pour l’offre.   Les champs ajoutés par un astérisque sont obligatoires.

|      Champ         |    Description    |
|  ---------------   |  ---------------  |
| **Titre\***        | Titre de l’offre. Il est affiché de façon visible dans la Place de marché. La longueur maximale est de 50 caractères. |
| **Résumé\***      | Court résumé de l’offre. La longueur maximale est de 100 caractères.           |
| **Résumé long\*** | Résumé plus détaillé de l’offre (il peut être identique au contenu du champ Résumé). La longueur maximale est de 256 caractères.           |
| **Description\***  | Description de l’offre. La longueur maximale est de 3 000 caractères. Mise en forme HTML simple, comprenant des balises&lt;p&gt;, &lt;em&gt;, &lt;ul&gt;, &lt;li&gt;, &lt;ol&gt; et header.  |
| **Identificateur de marketing\*** | URL unique à associer à cette offre. Elle inclut généralement le nom de votre solution et de votre organisation, avec une longueur maximale de 50 caractères. Choisissez un identificateur marketing court et convivial pour votre service. Il sera utilisé dans les URL de la Place de marché pour cette offre. Par exemple, si votre ID d’éditeur est « contoso » et votre identificateur marketing est « sampleApp », l’URL de votre offre dans la Place de marché Azure sera https://azuremarketplace.microsoft.com/en-us/marketplace/apps/contoso.sampleApp  
| **ID d’abonnement en version préliminaire\*** | Ajoutez entre 1 et 100 identificateurs d’abonnement d’utilisateurs de la préversion. Ces abonnements autorisés ont accès à l’offre dès qu’elle est disponible en préversion et après sa publication, avant sa mise en service.          |
| **Liens utiles**    | Si vous le souhaitez, vous pouvez fournir des liens vers diverses ressources pour les utilisateurs de votre offre, telles que la prise en charge, documentation, forums, etc.  Il est recommandé que vous ajoutez au moins un lien à votre documentation.            |
| **Suggéré catégories (5 max.)\*** | Sélectionnez une à cinq catégories. Les catégories sélectionnées servent à mapper votre offre aux catégories de produits disponibles sur la Place de marché Microsoft Azure et le portail Azure. Elles sont affichées sur les pages de navigation et la page des détails de votre produit. |
|  |  |


## <a name="marketing-artifacts"></a>Artefacts marketing

Le formulaire Artefacts marketing contient les champs obligatoires et facultatifs illustrés dans la capture d’écran suivante. Les champs obligatoires sont indiqués par un astérisque (*).

![Formulaire Artefacts marketing](./media/azureapp-marketplace-artifacts.png)

Le tableau suivant décrit les artefacts marketing.

|      Champ         |    Description    |
|  ---------------   |  ---------------  |
| **Petite\***        | Petit logo : Format PNG 40 x 40 pixels     |
| **Moyenne\***       | Logo de taille moyenne : pixels de 90 x 90 au format PNG    |
| **Grande\***        | Grand logo : pixels de 115 x 115 au format PNG   |
| **Wide\***         | Logo large : pixels de 255 x 115 au format PNG    |
| **Bannière**           | Bannière facultative : pixels 815 x 290 au format PNG. **Remarque :** Une fois l’icône de bannière chargée, elle ne peut pas être supprimée. |
| **Captures d’écran (Max 5)** |        Les captures d’écran sont affichées sur la page de détails de votre produit. Il s’agit d’un excellent moyen de communiquer visuellement sur les actions et le mode de fonctionnement de votre application. Vous pouvez afficher des diagrammes architecturaux ou des illustrations de cas d’usage, par exemple. Les captures d’écran sont facultatives, et limitées à 5 par référence (SKU). Pour ajouter une capture d'écran :<ul><li>Sélectionnez **+ Ajouter une capture d’écran** pour ouvrir la fenêtre Capture d’écran</li><li>**Nom** : entrez un nom ou titre (longueur maximale de 100 caractères).</li><li>**Charger** : chargez l’image. Elle doit être au format PNG, et d’une taille de 533 x 324 pixels.</li></ul>           |
| **Ajout d’une vidéo**      | Facultatif, les vidéos sont affichés sur votre page de détails du produit. Il s’agit d’un excellent moyen de communiquer visuellement sur les actions et le mode de fonctionnement de votre application. Pour ajouter une vidéo : <ul><li>Sélectionnez **+ Ajouter vidéo** pour ouvrir la fenêtre Vidéo</li><li>**Nom** : entrez un nom ou titre (longueur maximale de 100 caractères).</li><li>**Lien** : entrez l’URL du site qui héberge la vidéo (YouTube ou Vimeo)</li><li>**Miniature** : chargez une miniature. Elle doit être au format PNG, et d’une taille de 533 x 324 pixels.</li></ul>          |
|  |  |


### <a name="artifact-examples-in-azure-marketplace"></a>Exemples d’artefact dans la Place de marché Azure

La capture d’écran suivante montre un exemple de résultat de recherche dans la Place de marché.

![Résultat de recherche d’offre dans la Place de marché](./media/azureapp-marketplace-example-browse.png)

L’illustration suivante montre comment l’offre apparaît sur la Place de marché lorsqu'un client clique sur le titre de l'offre dans le résultat de la recherche.

![Détails du résultat de recherche d’offre dans la Place de marché](./media/azureapp-marketplace-example-details.png)


### <a name="artifact-examples-in-azure-portal"></a>Exemples d’artefact dans le portail Azure

Les captures d’écran suivantes montrent comment une offre s’affiche dans le portail Azure. L’offre d’application dans cet exemple est accessible via **Place de marché>Tout>Développement + Test>Jenkins**. L’offre Jenkins affiche un logo, un titre et le nom d’affichage de l’éditeur.

![Parcourir les offres dans le portail Azure](./media/azureapp-portalbrowse-artifacts-jenkins.png)

La capture d’écran suivante montre des informations détaillées sur l’application lorsqu’un utilisateur sélectionne Jenkins.

![Détails de l’offre dans le portail Azure](./media/azureapp-portal-artifacts-jenkins-details.png)


### <a name="logo-guidelines"></a>Instructions concernant le logo

Tous les logos chargés sur le Portail Cloud Partner doivent respecter ces instructions :

- Le design Azure a une palette de couleurs simple. Limitez le nombre de couleurs primaires et secondaires sur votre logo.
- Les couleurs de thème du Portail Azure sont le blanc et le noir. Évitez d’utiliser ces couleurs comme couleur d’arrière-plan pour vos logos. Utilisez des couleurs qui font ressortir vos logos sur le Portail Azure. Nous vous recommandons d’utiliser des couleurs primaires simples. Si vous utilisez un arrière-plan transparent, assurez-vous que les logos/le texte ne sont pas blanc, noir ou bleu.
- N’utilisez pas d’arrière-plan dégradé sur votre logo.
- Évitez de placer du texte, même s’il s’agit du nom votre société ou de votre marque, sur le logo. L’apparence de votre logo doit être « plate » et éviter les dégradés.
- N’étirez pas le logo.


#### <a name="hero-logo"></a>Bannière

La bannière est facultative.

>[!IMPORTANT]
>Une fois chargée, vous ne pouvez pas supprimer la bannière.

Pour un logo de bannière, suivez les instructions ci-après :

- Les arrière-plans noirs, blancs et transparents ne sont pas autorisés.
- Évitez d’utiliser une couleur claire dans l’arrière-plan du logo. Le nom d’affichage de l’éditeur, le titre du plan et le résumé long de l’offre s’affichent en couleur de police blanche et doivent ressortir par rapport à l’arrière-plan.
- Évitez d’utiliser du texte quand vous créez le logo. Le nom de l’éditeur, le titre du plan, le résumé long de l’offre et un bouton Créer sont incorporés par programmation à l’intérieur du logo quand l’offre est répertoriée.
- Ajoutez un espace rectangulaire non utilisé sur le côté droit de votre logo de bannière. Cet espace vide est de 415 x 100 pixels, et le décalage à partir de la gauche de 370 pixels.


## <a name="lead-management"></a>Gestion des prospects

Le formulaire Gestion des prospects a un champ facultatif pour configurer la gestion des prospects. Pour configurer la gestion des prospects, sélectionnez la destination Prospect dans la liste déroulante. La capture d’écran suivante montre les destinations disponibles.

![Sélectionnez la destination de la gestion des prospects](./media/azureapp-marketplace-leadmgmt.png)

>[!TIP]
>Sélectionnez l’icône d’information pour afficher ce message : « Sélectionnez le système dans lequel vos prospects seront stockés. Apprenez à connecter votre système CRM [ici](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads). »

Pour plus d’informations, voir [Configurer des prospects](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads).


## <a name="legal"></a>Informations juridiques

Utilisez le formulaire Informations juridiques pour fournir la documentation juridique requise pour toutes les offres.

Fournissez les informations suivantes :

- **URL de stratégie de confidentialité\***  – Entrez un lien vers la politique de confidentialité de votre application.
- **Conditions d’utilisation\***  – Entrez les conditions d’utilisation pour votre application. Les clients doivent accepter ces conditions avant de pouvoir essayer de votre application.

![Formulaire Informations juridiques](./media/azureapp-marketplace-legal.png)


## <a name="next-steps"></a>Étapes suivantes

[Onglet du support](./cpp-support-tab.md)
