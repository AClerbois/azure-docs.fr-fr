---
title: Actualiser avec Logic Apps pour les modèles Azure Analysis Services | Microsoft Docs
description: En savoir plus sur le codage de l’actualisation asynchrone à l’aide d’Azure Logic Apps.
author: chrislound
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: chlound
ms.openlocfilehash: 6ffce339fe7b1a434c8f007b417ee81a42529dfc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66142448"
---
# <a name="refresh-with-logic-apps"></a>Actualiser avec Logic Apps

Grâce à Logic Apps et aux appels REST, vous pouvez effectuer des opérations d’actualisation de données automatiques sur vos modèles tabulaires Azure Analysis, notamment la synchronisation de réplicas en lecture seule pour la montée en charge (scale-out) des requêtes.

Pour en savoir plus sur l’utilisation des API REST avec Azure Analysis Services, consultez [Actualisation asynchrone avec l’API REST](analysis-services-async-refresh.md).

## <a name="authentication"></a>Authentication

Tous les appels doivent être authentifiés avec un jeton Azure Active Directory (OAuth 2) valide.  Les exemples de cet article utilisent un principal du service (SPN) pour s’authentifier auprès d’Azure Analysis Services. Pour en savoir plus, consultez [Créer un principal du service à l’aide du portail Azure](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="design-the-logic-app"></a>Concevoir l’application logique

> [!IMPORTANT]
> Les exemples suivants supposent que le pare-feu Azure Analysis Services est désactivé.  Si le pare-feu est activé, l’adresse IP publique de l’initiateur de la requête doit figurer sur la liste verte dans le pare-feu Azure Analysis Services. Pour en savoir plus sur les plages d’adresses IP d’application logique par région, consultez [Limites et informations de configuration pour Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#firewall-configuration-ip-addresses).

### <a name="prerequisites"></a>Prérequis

#### <a name="create-a-service-principal-spn"></a>Créer un principal du service (SPN)

Pour en savoir plus sur la création d’un principal de service, consultez [Créer un principal du service à l’aide du portail Azure](../active-directory/develop/howto-create-service-principal-portal.md).

#### <a name="configure-permissions-in-azure-analysis-services"></a>Configurer des autorisations dans Azure Analysis Services
 
Le principal du service que vous créez doit disposer d’autorisations d’administrateur du serveur sur le serveur. Pour plus d’informations, consultez [Ajouter un principal de service au rôle d’administrateur du serveur](analysis-services-addservprinc-admins.md)

### <a name="configure-the-logic-app"></a>Configurer l’application logique

Dans cet exemple, l’application logique est conçue pour être déclenchée lors de la réception d’une requête HTTP. Cela permet d’utiliser un outil d’orchestration, tel qu’Azure Data Factory, pour déclencher l’actualisation du modèle Azure Analysis Services.

Une fois que vous avez créé une application logique :

1. Dans le concepteur d’application logique, choisissez la première action **Lors de la réception d’une requête HTTP**.

   ![Ajouter une activité de réception HTTP](./media/analysis-services-async-refresh-logic-app/1.png)

Cette étape est remplie avec l’URL HTTP POST une fois que l’application logique est enregistrée.

2. Ajoutez une nouvelle étape et recherchez **HTTP**.  

   ![Ajouter une activité HTTP](./media/analysis-services-async-refresh-logic-app/9.png)

   ![Ajouter une activité HTTP](./media/analysis-services-async-refresh-logic-app/10.png)

3. Sélectionnez **HTTP** pour ajouter cette action.

   ![Ajouter une activité HTTP](./media/analysis-services-async-refresh-logic-app/2.png)

Configurez l’activité HTTP comme suit :

|Propriété  |Valeur  |
|---------|---------|
|**Méthode**     |POST         |
|**URI**     | https://*région de votre serveur*/servers/*nom du serveur aas*/models/*nom de votre base de données*/ <br /> <br /> Par exemple :  https:\//westus.asazure.windows.net/servers/myserver/models/AdventureWorks/|
|**En-têtes**     |   Content-Type, application/json <br /> <br />  ![headers](./media/analysis-services-async-refresh-logic-app/6.png)    |
|**Corps**     |   Pour en savoir plus sur la formation de corps de la demande, consultez [Actualisation asynchrone avec l’API REST - POST /refreshes](analysis-services-async-refresh.md#post-refreshes). |
|**Authentification**     |OAuth Active Directory         |
|**Locataire**     |Indiquez votre ID de locataire Azure Active Directory         |
|**Public ciblé**     |https://*.asazure.windows.net         |
|**ID client**     |Entrez votre ID client de nom de principal du service         |
|**Type d’informations d’identification**     |Secret         |
|**Secret**     |Entrez votre secret de nom de principal du service         |

Exemple :

![Activité HTTP terminée](./media/analysis-services-async-refresh-logic-app/7.png)

Testez maintenant l’application logique.  Dans le concepteur d’application logique, cliquez sur **Exécuter**.

![Tester l’application logique](./media/analysis-services-async-refresh-logic-app/8.png)

## <a name="consume-the-logic-app-with-azure-data-factory"></a>Utiliser l’application logique avec Azure Data Factory

Une fois que l’application logique est enregistrée, passez en revue l’activité **Lors de la réception d’une requête HTTP**, puis copiez l’**URL HTTP POST** qui est désormais générée.  Il s’agit de l’URL qui peut être utilisée par Azure Data Factory pour effectuer l’appel asynchrone afin de déclencher l’application logique.

Voici un exemple d’activité web Azure Data Factory qui effectue cette action.

![Activité web Data Factory](./media/analysis-services-async-refresh-logic-app/11.png)

## <a name="use-a-self-contained-logic-app"></a>Utiliser une application logique autonome

Si vous ne prévoyez pas d’utiliser un outil d’orchestration tel que Data Factory pour déclencher l’actualisation de modèle, vous pouvez définir l’application logique pour qu’elle déclenche l’actualisation selon une planification.

À l’aide de l’exemple ci-dessus, supprimez la première activité et remplacez-la par une activité **Planification**.

![Activité de planification](./media/analysis-services-async-refresh-logic-app/12.png)

![Activité de planification](./media/analysis-services-async-refresh-logic-app/13.png)

Cet exemple utilise **Périodicité**.

Une fois que l’activité a été ajoutée, configurez l’intervalle et la fréquence, puis ajoutez un nouveau paramètre et choisissez **Aux heures indiquées**.

![Activité de planification](./media/analysis-services-async-refresh-logic-app/16.png)

Sélectionnez les heures souhaitées.

![Activité de planification](./media/analysis-services-async-refresh-logic-app/15.png)

Enregistrez l’application logique.

## <a name="next-steps"></a>Étapes suivantes

[Exemples](analysis-services-samples.md)  
[API REST](https://docs.microsoft.com/rest/api/analysisservices/servers)
