---
title: Publier une offre de services managés sur la Place de marché Azure
description: Découvrez comment publier une offre de service managé qui intègre des clients à la gestion des ressources déléguées Azure.
author: JnHs
ms.author: jenhayes
ms.service: lighthouse
ms.date: 08/29/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: c0c2ccf03292434b3f23b26857ec0d2b3fc3ceed
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70165266"
---
# <a name="publish-a-managed-services-offer-to-azure-marketplace"></a>Publier une offre de services managés sur la Place de marché Azure

Cet article explique comment publier une offre de services managés publics ou privés sur la [Place de marché Azure](https://azuremarketplace.microsoft.com) via le [portail Cloud Partner](https://cloudpartner.azure.com/), qui permet à un client qui achète l’offre d’intégrer des ressources pour la gestion des ressources déléguées Azure.

> [!NOTE]
> Pour créer et publier ces offres, vous devez disposer d’un [compte valide dans l’Espace partenaires](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account). Si vous n’avez pas encore de compte, le [processus d’inscription](https://aka.ms/joinmarketplace) vous guide dans les étapes de création de compte dans l’Espace partenaires et d’inscription au programme de la Place de marché commerciale. Votre ID Microsoft Partner Network (MPN) est [automatiquement associé](https://docs.microsoft.com/azure/billing/billing-partner-admin-link-started) aux offres que vous publiez pour suivre votre impact sur les engagements client.
>
> Si vous ne souhaitez pas publier d’offre sur la Place de marché Azure, vous pouvez intégrer des clients manuellement à l’aide de modèles Azure Resource Manager. Pour plus d’informations, voir [Intégrer un client dans la gestion des ressources déléguées Azure](onboard-customer.md).

La publication d’une offre de services managés est similaire à la publication d’un autre type d’offre sur la Place de marché Azure. Pour découvrir ce processus, voir [Guide de publication de la Place de marché Azure et AppSource](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide) et [Gérer les offres sur les places de marché Azure et AppSource](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/manage-offers/cpp-manage-offers). Vous devez également examiner les [stratégies de certification de la Place de marché commerciale](https://docs.microsoft.com/legal/marketplace/certification-policies), en particulier la section [Managed Services](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services).

> [!IMPORTANT]
> Chaque plan d’une offre de services managés comprend une section **Détails du manifeste** dans laquelle vous définissez les entités Azure Active Directory (Azure AD) de votre locataire qui ont accès aux groupes de ressources ou aux abonnements délégués pour les clients qui achètent ce plan. Il est important de savoir que tout groupe (ou utilisateur ou principal de service) que vous incluez ici offrira les mêmes autorisations à chaque client achetant le plan. Pour affecter différents groupes à chaque client, vous devez publier un plan privé distinct exclusif pour chaque client.

## <a name="create-your-offer-in-the-cloud-partner-portal"></a>Créer votre offre dans le portail Cloud Partner

1. Connectez-vous au [portail Cloud Partner](https://cloudpartner.azure.com/).
2. Dans le menu de navigation de gauche, sélectionnez **Nouvelle offre**, puis **Services managés**.
3. Une fenêtre **Éditeur** s’affiche pour votre offre, comportant quatre sections à remplir : **Paramètres de l’offre**, **Plans**, **Place de marché** et **Support**. Lisez la suite pour obtenir des instructions sur la façon de compléter ces sections.

## <a name="enter-offer-settings"></a>Entrer les paramètres de l’offre

Dans la section **Paramètres de l’offre**, entrez les informations suivantes :

|Champ  |Description  |
|---------|---------|
|**ID de l’offre**     | Identificateur unique de votre offre au sein de votre profil d’éditeur. Cet ID ne peut contenir que des caractères alphanumériques minuscules, des tirets et des traits de soulignement, dont le nombre ne peut pas dépasser 50 signes. N’oubliez pas que l’ID de l’offre peut être visible pour les clients dans des emplacements tels que des URL et des états de facturation de produit. Une fois l’offre publiée, vous ne pouvez plus modifier cette valeur.        |
|**ID de l’éditeur**     | ID de l’éditeur qui sera associé à l’offre. Si vous avez plus d’un ID d’éditeur, vous pouvez sélectionner celui que vous souhaitez utiliser pour cette offre.       |
|**Nom**     | Nom (jusqu’à 50 caractères) que les clients verront pour votre offre sur la Place de marché et le portail Azure. Utilisez un nom de marque reconnaissable que les clients comprendront. Si vous promouvez cette offre via votre propre site web, veillez à utiliser exactement le même nom ici.        |

Quand vous avez terminé, sélectionnez **Enregistrer**. Vous êtes désormais prêt à passer à la section **Plans**.

## <a name="create-plans"></a>Créer des plans

Chaque offre doit comprendre un ou plusieurs plans (parfois appelés références SKU). Vous pouvez ajouter plusieurs plans correspondant à différents ensembles de fonctionnalités proposés à différents prix, ou personnaliser un plan spécifique pour un public limité de clients spécifiques. Les clients peuvent afficher les plans qui sont à leur disposition sous l’offre parente.

Dans la section Plans, pour chaque plan que vous souhaitez créer, sélectionnez **Nouveau plan**. Entrer ensuite un **ID de plan**. Cet ID ne peut contenir que des caractères alphanumériques minuscules, des tirets et des traits de soulignement, dont le nombre ne peut pas dépasser 50 signes. L’ID de plan peut être visible pour les clients dans des emplacements tels que les URL et états de facturation de produit. Une fois l’offre publiée, vous ne pouvez plus modifier cette valeur.

Ensuite, complétez les sections suivantes dans **Détails du plan** :

|Champ  |Description  |
|---------|---------|
|**Titre**     | Nom convivial du plan à afficher. Longueur maximale de 50 caractères.        |
|**Résumé**     | Description succincte du plan à afficher sous le titre. Longueur maximale de 100 caractères.        |
|**Description**     | Texte de description fournissant davantage de détails sur le plan.         |
|**Modèle de facturation**     | Deux modèles de facturation sont présentés ici, mais vous devez choisir **BYOL (apportez votre propre licence)** pour les offres de services managés. Cela signifie que vous facturez directement à vos clients les coûts liés à l’offre, et que Microsoft ne vous facture aucuns frais.   |
|**Ce plan est-il privé ?**     | Indique si la référence SKU est privée ou publique. La valeur par défaut est **Non** (publique). Si vous conservez cette sélection, votre plan ne sera pas limité à des clients spécifiques (ou à un certain nombre de clients). Une fois que vous avez publié un plan public, vous ne pouvez plus le modifier en plan privé par la suite. Pour que ce plan soit disponible uniquement pour des clients spécifiques, sélectionnez **Oui**. Dans ce cas, vous devez identifier les clients en fournissant leurs ID d’abonnement. Vous pouvez entrer ceux-ci un par un (pour jusqu’à 10 abonnements) ou en chargeant un fichier .csv (pour jusqu’à 20 000 abonnements). Veillez à inclure vos propres abonnements ici afin de pouvoir tester et valider l’offre. Pour plus d’informations, voir [Références SKU et plans privés](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus).  |

Enfin, complétez la section **Détails du manifeste**. Cela a pour effet de créer un manifeste contenant des informations d’autorisation pour la gestion des ressources du client. Les informations que vous fournissez ici sont nécessaires pour l’intégration de vos clients dans le cadre de la gestion des ressources déléguées Azure. Comme indiqué ci-dessus, ces autorisations s’appliquent à chaque client qui achète le plan de sorte que, si vous souhaitez limiter l’accès à un client spécifique, vous devez publier un plan privé à son usage exclusif.

- Tout d’abord, fournissez une **Version** pour le manifeste. Utilisez le format *n.n.n* (par exemple, 1.2.5).
- Ensuite, entrez votre **ID de locataire**. Il s’agit d’un GUID associé à l’ID de locataire Azure Active Directory de votre organisation (par exemple, le locataire dans lequel vous allez travailler pour gérer les ressources de vos clients). Si vous ne l’avez pas sous la main, vous pouvez le trouver en pointant sur le nom de votre compte dans l’angle supérieur droit du portail Azure ou en sélectionnant **Changer de répertoire**. 
- Enfin, ajoutez une ou plusieurs entrées **Autorisation** à votre plan. Les autorisations définissent les entités qui peuvent accéder aux ressources et aux abonnements pour les clients qui achètent le plan. Vous devez fournir ces informations afin d’accéder aux ressources pour le compte du client à l’aide de la gestion des ressources déléguées Azure.
  Pour chaque autorisation, fournissez les éléments suivants. Vous pouvez ensuite sélectionner **Nouvelle autorisation** autant de fois que nécessaire pour ajouter des utilisateurs/définitions de rôles.
  - **ID d’objet Azure AD** : identificateur Azure AD d’un utilisateur, d’un groupe d’utilisateurs ou d’une application auxquels certaines autorisations seront accordées (comme indiqué dans la définition de rôle) sur les ressources de votre client.
  - **Nom d’affichage d’objet Azure AD** : nom convivial destiné à aider le client à comprendre l’objectif de cette autorisation. Le client verra ce nom lors de la délégation de ressources.
  - **Définition de rôle** : sélectionnez l’un des rôles intégrés Azure AD disponibles dans la liste. Ce rôle détermine les autorisations sur les ressources de vos clients dont disposera l’utilisateur spécifié dans le champ **ID d’objet Azure AD**. Pour plus d’informations sur ces rôles, voir [Rôles intégrés](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).
  - **Rôles attribuables** : si vous avez sélectionné Administrateur de l’accès utilisateur dans la **Définition de rôle** pour cette autorisation, vous pouvez ajouter un ou plusieurs rôles attribuables ici. L’utilisateur indiqué dans le champ **ID d’objet Azure AD** sera en mesure d’attribuer ces **Rôles attribuables** à des [identités managées](https://docs.microsoft.com/azure/managed-applications/publish-managed-identity). Notez qu’aucune autre autorisation normalement associée au rôle Administrateur de l’accès utilisateur ne s’appliquera à cet utilisateur (si vous n’avez pas sélectionné Administrateur de l’accès utilisateur pour la définition de rôle de cet utilisateur, ce champ est sans effet).

> [!TIP]
> Dans la plupart des cas, vous affectez des autorisations à un groupe d’utilisateurs ou à un principal de service Azure AD, plutôt qu’à une série de comptes d’utilisateur individuels. Cela vous permet d’ajouter ou de supprimer l’accès d’utilisateurs individuels sans devoir mettre à jour et republier le plan lorsque vos conditions d’accès changent.

Lorsque vous avez terminé d’ajouter des plans, sélectionnez **Enregistrer**, puis passez à la section **Place de marché**.

## <a name="provide-marketplace-text-and-images"></a>Fournir du texte et des images à la Place de marché

La section **Place de marché** est l’endroit où vous fournissez le texte et les images que les clients verront sur la Place de marché et le portail Azure.

Renseignez les champs suivants dans la section **Vue d’ensemble** :

|Champ  |Description  |
|---------|---------|
|**Titre**     |  Titre de l’offre. Il s’agit souvent du nom formel long. Il est affiché de façon visible dans la Place de marché. Longueur maximale de 50 caractères. Dans la plupart des cas, il doit s’agir du **Nom** que vous avez entré dans la section **Paramètres de l’offre**.       |
|**Résumé**     | Brève description de la finalité ou de la fonction de votre offre. Généralement affiché sous le titre. Longueur maximale de 100 caractères.        |
|**Long Summary** (Résumé long)     | Récapitulatif plus long de la finalité ou de la fonction de votre offre. Longueur maximale de 256 caractères.        |
|**Description**     | Informations supplémentaires sur votre offre. Ce champ peut compter jusqu’à 3 000 caractères et prend en charge la mise en forme HTML simple. Vous devez inclure les mots « service managé » ou « services managés » quelque part dans votre description.       |
|**Marketing Identifier** (Identificateur marketing)     | Identificateur unique compatible avec les URL. Il sera utilisé dans les URL de la Place de marché pour cette offre. Par exemple, si votre ID d’éditeur est *contoso* et votre identificateur marketing *sampleApp*, l’URL de votre offre sur la Place de marché Azure sera *https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sampleApp* .        |
|**ID d’abonnement pour préversion**     | Ajoutez entre 1 et 100 identificateurs d’abonnement. Les clients associés à ces abonnements pourront afficher l’offre sur la Place de marché Azure avant sa mise en ligne. Nous vous suggérons d’inclure ici vos propres abonnements afin de pouvoir prévisualiser l’apparence de votre offre sur la Place de marché Azure avant de la mettre à la disposition des clients  (le support technique Microsoft et les équipes d’ingénierie pourront également voir votre offre pendant cette période de préversion).   |
|**Liens utiles**     | URL associées à votre offre, telles celles de la documentation, des notes de publication, du FAQ, etc.        |
|**Catégories suggérées (cinq au maximum)**     | Une ou plusieurs catégories (jusqu’à cinq) s’appliquant à votre offre. Ces catégories aident les clients à découvrir votre offre sur la Place de marché et le portail Azure.        |

Dans la **Marketing Artifacts** (Artefacts marketing), vous pouvez charger des logos et d’autres ressources à afficher avec votre offre. Vous pouvez éventuellement charger des captures d’écran ou des liens vers des vidéos susceptibles d’aider les clients à comprendre votre offre.

Quatre tailles de logo sont requises : **Petite (40 x 40)** , **Moyenne (90 x 90)** , **Grande (115 x 115)** et **Large (255 x 155)** . Suivez ces instructions pour vos logos:

- Le design Azure a une palette de couleurs simple. Limitez le nombre de couleurs primaires et secondaires sur votre logo.
- Les couleurs de thème du portail sont le blanc et le noir. Évitez d’utiliser ces couleurs comme couleur d’arrière-plan pour votre logo. Utilisez une couleur qui donne à votre logo plus de visibilité dans le portail. Nous vous recommandons d’utiliser des couleurs primaires simples.
- Si vous utilisez un arrière-plan transparent, assurez-vous que le logo et le texte ne sont pas blancs, noirs ou bleus.
- L'apparence de votre logo doit être « plate » et éviter les dégradés. N'utilisez pas d’arrière-plan dégradé sur le logo.
- Ne placez pas de texte sur le logo, pas même le nom de votre marque ou de votre entreprise.
- Assurez-vous que le logo n’est pas étiré.

Le logo **Bannière (815 x 290**) est facultatif mais recommandé. Si vous incluez un logo bannière, suivez les instructions ci-dessous :

- N’incluez pas de texte dans le logo bannière et veillez à conserver 415 pixels d’espace vide sur le côté droit du logo. Cela est nécessaire pour ménager de la place pour les éléments de texte qui seront incorporés par programme, à savoir votre nom d’affichage d’éditeur, le titre du plan, le résumé long de l’offre.
- L’arrière-plan de votre logo bannière peut ne pas être noir, blanc ou transparent. Assurez-vous que la couleur d’arrière-plan n’est pas trop claire, car le texte incorporé s’affichera en blanc.
- Une fois que vous avez publié votre offre avec une icône de bannière, vous ne pouvez plus la supprimer (même si vous pouvez la mettre à jour avec une autre version si vous le souhaitez).

Dans la section **Lead Management** (Gestion des prospects), vous pouvez sélectionner le système CRM dans lequel vos prospects seront stockés. Notez que, selon [les stratégies de certification des services managés](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services), une **destination de prospect** est requise.

Enfin, entrez votre **Privacy Policy URL** (URL de la politique de confidentialité) et vos **Terms of use** (Conditions d’utilisation) dans la section **Legal** (Légal). Vous pouvez également spécifier ici s’il faut ou non utiliser le [contrat standard](https://docs.microsoft.com/azure/marketplace/standard-contract) pour cette offre.

Veillez à enregistrer vos modifications avant de passer à la section **Support**.

## <a name="add-support-info"></a>Ajouter des informations de support

Dans la section **Support**, entrez le nom, l’adresse e-mail et le numéro de téléphone d’un contact d’ingénierie et d’un contact du service clientèle. Vous devez également fournir des URL de support. Microsoft peut utiliser ces informations en cas de nécessité de vous contacter à propos d’éventuels problèmes d’exploitation et de support.

Une fois que vous avez ajouté ces informations, sélectionnez **Enregistrer**.

## <a name="publish-your-offer"></a>Publier votre offre

Lorsque vous êtes satisfait de toutes les informations que vous avez fournies, l’étape suivante consiste à publier l’offre sur la Place de marché Azure. Sélectionnez le bouton **Publier** pour démarrer le processus de mise en ligne de votre offre. Pour plus d’informations sur ce processus, voir [Publier des offres sur la Place de marché Microsoft Azure et AppSource](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/manage-offers/cpp-publish-offer).

## <a name="the-customer-onboarding-process"></a>Processus d’intégration des clients

Lorsqu’un client ajoute votre offre, il est en mesure de [déléguer un ou plusieurs abonnements ou groupes de ressources spécifiques](view-manage-service-providers.md#delegate-resources) qui seront ensuite intégrés pour la gestion des ressources déléguées Azure. Si un client a accepté une offre mais n’a pas encore délégué de ressources, il voit une note s’afficher en haut de la section **Offres de fournisseur** de la page [**Fournisseurs de services**](view-manage-service-providers.md) du portail Azure.

Pour qu’un abonnement (ou des groupes de ressources au sein d’un abonnement) puisse être intégré, l’intégration de l’abonnement doit être autorisée via une inscription manuelle du fournisseur de ressources **Microsoft.ManagedServices**. Un utilisateur figurant dans le locataire du client et doté du rôle Contributeur ou Propriétaire peut le faire en suivant les étapes décrites dans [Fournisseurs et types de ressources Azure](../../azure-resource-manager/resource-manager-supported-services.md).

Le client peut alors confirmer que l’abonnement est prêt à être intégré de l’une des manières suivantes.

### <a name="azure-portal"></a>Portail Azure

1. Dans le Portail Azure, sélectionnez l’abonnement.
1. Sélectionnez **Fournisseurs de ressources**.
1. Vérifiez que **Microsoft.ManagedServices** apparaît comme **Inscrit**.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Set-AzContext -Subscription <subscriptionId>
Get-AzResourceProvider -ProviderNameSpace 'Microsoft.ManagedServices'
```

Vous devriez obtenir des résultats similaires à ceci :

```output
ProviderNamespace : Microsoft.ManagedServices
RegistrationState : Registered
ResourceTypes     : {registrationDefinitions}
Locations         : {}

ProviderNamespace : Microsoft.ManagedServices
RegistrationState : Registered
ResourceTypes     : {registrationAssignments}
Locations         : {}

ProviderNamespace : Microsoft.ManagedServices
RegistrationState : Registered
ResourceTypes     : {operations}
Locations         : {}
```

### <a name="azure-cli"></a>D’Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account set –subscription <subscriptionId>
az provider show --namespace "Microsoft.ManagedServices" --output table
```

Vous devriez obtenir des résultats similaires à ceci :

```output
Namespace                  RegistrationState
-------------------------  -------------------
Microsoft.ManagedServices  Registered
```

## <a name="next-steps"></a>Étapes suivantes

- Découvrez les [Expériences de gestion inter-locataire](../concepts/cross-tenant-management-experience.md).
- [Affichez et gérez les clients](view-manage-customers.md) en accédant à **Mes clients** sur le portail Azure.
