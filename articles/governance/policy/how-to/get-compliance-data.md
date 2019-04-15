---
title: Obtenir les données de conformité de la stratégie
description: Les évaluations et les effets d’Azure Policy déterminent la conformité. Découvrez comment obtenir des détails sur la conformité.
author: DCtheGeek
ms.author: dacoulte
ms.date: 02/01/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 84ed1632a61ae097bd2e187de4766dfc50f2503f
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59263768"
---
# <a name="get-compliance-data-of-azure-resources"></a>Obtenir des données de conformité des ressources Azure

Azure Policy offre, entre autres avantages, un insight et des contrôles sur les ressources d’un abonnement ou un [groupe d’administration](../../management-groups/overview.md) d’abonnements. Ce contrôle peut être effectué de différentes façons, notamment en empêchant la création de ressources au mauvais emplacement, en appliquant une utilisation commune et cohérente des balises ou en auditant les ressources existantes pour vérifier l’adéquation des configurations et paramètres. Dans tous les cas, les données générées par Azure Policy vous permettent de comprendre l’état de conformité de votre environnement.

Il existe plusieurs façons d’accéder aux informations de conformité générées par votre stratégie et vos affectations initiatives :

- À l’aide du [portail Azure](#portal)
- À l’aide de scripts de [ligne de commande](#command-line)

Avant d’examiner les méthodes de rapport sur la conformité, voyons à quel moment les informations de conformité sont mises à jour et passons en revue la fréquence et les événements qui déclenchent un cycle d’évaluation.

> [!WARNING]
> Si l’état de conformité est signalé comme **ne pas inscrit**, vérifiez que le **Microsoft.PolicyInsights** (de contrôle de fournisseur de ressources est inscrit et que l’utilisateur dispose de l’accès approprié en fonction du rôle Les autorisations RBAC) comme décrit dans [RBAC dans Azure Policy](../overview.md#rbac-permissions-in-azure-policy).

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="evaluation-triggers"></a>Déclencheurs d’évaluation

Les résultats d’un cycle d’évaluation terminé sont disponibles dans le fournisseur de ressources `Microsoft.PolicyInsights` via les opérations `PolicyStates` et `PolicyEvents`. Pour plus d’informations sur les opérations de l’API REST de stratégie Insights, consultez la page [Policy Insights (Stratégie Insights)](/rest/api/policy-insights/).

Différents événements permettent d’évaluer les stratégies et initiatives assignées :

- Affectation d’une nouvelle stratégie ou initiative à une étendue. Il faut environ 30 minutes pour que l’affectation soit appliquée à l’étendue définie. Une fois celle-ci appliquée, le cycle d’évaluation commence pour les ressources de cette étendue compte tenu de la nouvelle stratégie ou initiative ; de plus, selon les effets utilisés par la stratégie ou l’initiative, les ressources sont marquées comme conformes ou non conformes. Une stratégie ou une initiative volumineuse évaluée par rapport à une grande étendue de ressources peut prendre du temps. Par conséquent, il n’existe aucun moment prédéfini pour la fin du cycle d’évaluation. Une fois le cycle terminé, les résultats de conformité à jour sont disponibles dans le portail et dans les kits de développement logiciel.

- Mise à jour d’une stratégie ou initiative déjà assignée à une étendue. Dans ce scénario, le cycle et le temps d’évaluation sont les mêmes que pour le cas d’une nouvelle affectation à une étendue.

- Déploiement d’une ressource dans une étendue avec une assignation via le Gestionnaire des ressources, REST, Azure CLI ou Azure PowerShell. Dans ce scénario, l’événement d’effet (ajout, audit, refus, déploiement) et l’état de conformité deviennent disponibles dans le portail et les Kits de développement logiciel (SDK) environ 15 minutes plus tard. Cet événement n’entraîne pas une évaluation des autres ressources.

- Cycle d’évaluation de conformité standard. Les affectations sont automatiquement réévaluées une fois par tranche de 24 heures. L’évaluation d’une stratégie ou d’une initiative volumineuse peut prendre un temps. Il est donc impossible de déterminer à l’avance à quel moment s’achèvera le cycle d’évaluation. Une fois le cycle terminé, les résultats de conformité à jour sont disponibles dans le portail et dans les kits de développement logiciel.

- Le fournisseur de ressources [Configuration d'invité](../concepts/guest-configuration.md) est mis à jour avec les détails de conformité par une ressource managée.

- Analyse de la demande

### <a name="on-demand-evaluation-scan"></a>Analyse d’évaluation à la demande

Une analyse d’évaluation d’un abonnement ou d’un groupe de ressources peut être démarrée avec un appel à l’API REST. Il s’agit d’un processus asynchrone. Par conséquent, le point de terminaison REST pour démarrer l’analyse n’attend pas la fin de l’analyse pour répondre. Au lieu de cela, il fournit un URI pour interroger l’état de l’évaluation demandée.

Dans chaque URI d’API REST, vous devez remplacer les variables utilisées par vos propres valeurs :

- `{YourRG}` -Remplacer par le nom de votre groupe de ressources
- `{subscriptionId}` -Remplacer par votre ID d’abonnement

L’analyse prend en charge l’évaluation des ressources dans un abonnement ou dans un groupe de ressources. Lancez une analyse pour une étendue avec une commande **POST** d’API REST en utilisant les structures d’URI suivantes :

- Abonnement

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2018-07-01-preview
  ```

- Groupe de ressources

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{YourRG}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2018-07-01-preview
  ```

L’appel retourne un état **202 Accepté**. Une propriété **Emplacement** est incluse dans l’en-tête de la réponse avec le format suivant :

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/asyncOperationResults/{ResourceContainerGUID}?api-version=2018-07-01-preview
```

`{ResourceContainerGUID}` est statiquement généré pour la portée demandée. Si une étendue exécute déjà une analyse à la demande, aucune nouvelle analyse n’est démarrée. Au lieu de cela, le même URI `{ResourceContainerGUID}`**d’emplacement** pour l’état est fourni à la nouvelle requête. Une commande **GET** d’API REST à l’URI **d’emplacement** retourne un **202 Accepté** tandis que l’évaluation est en cours. Une fois l’analyse de l’évaluation terminée, elle retourne un état **200 OK**. Le corps d’une analyse terminée est une réponse JSON avec l’état :

```json
{
    "status": "Succeeded"
}
```

## <a name="how-compliance-works"></a>Principe de fonctionnement de la conformité

Dans une affectation, une ressource est dite **Non conforme** si elle ne respecte pas les règles de l’initiative ou de la stratégie. Le tableau suivant montre comment les différents effets des stratégies fonctionnent avec l’évaluation des conditions pour l’état de conformité résultant :

| État de la ressource | Résultat | Évaluation de la stratégie | État de conformité |
| --- | --- | --- | --- |
| Exists | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | True | Non conforme |
| Exists | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | False | Conforme |
| Nouveau | Audit, AuditIfNotExist\* | True | Non conforme |
| Nouveau | Audit, AuditIfNotExist\* | False | Conforme |

\* Les effets Append, DeployIfNotExist et AuditIfNotExist nécessitent que l’instruction IF ait la valeur TRUE.
Les effets nécessitent également que la condition d’existence ait la valeur FALSE pour être non conformes. Lorsque la valeur est TRUE, la condition IF déclenche l’évaluation de la condition d’existence pour les ressources associées.

Supposons, par exemple, que vous disposiez d’un groupe de ressources (ContosoRG), comprenant des comptes de stockage (en rouge) qui sont exposés sur des réseaux publics.

![Comptes de stockage exposés sur des réseaux publics](../media/getting-compliance-data/resource-group01.png)

Dans cet exemple, vous devez faire attention aux risques de sécurité. Maintenant que vous avez créé une affectation de stratégie, elle est évaluée pour tous les comptes de stockage du groupe de ressources ContosoRG. Elle effectue l’audit des trois comptes de stockage non conformes et en modifie l’état en conséquence pour afficher un état **Non conforme**.

![Audit des comptes de stockage non conformes](../media/getting-compliance-data/resource-group03.png)

Outre les états **Conforme** et **Non conforme**, les stratégies et les ressources peuvent avoir trois autres états :

- **En conflit** : Il existe deux ou stratégies ou plus avec des règles en conflit. Par exemple, deux stratégies ajoutent la même balise avec des valeurs différentes.
- **Non démarré** : Le cycle d’évaluation n’a pas démarré pour la stratégie ou la ressource.
- **Non inscrit** : Le fournisseur de ressources Azure Policy n’a pas été inscrit ou le compte connecté n’est pas autorisé à lire les données de conformité.

La stratégie utilise les champs **type** et **nom** de la définition pour déterminer si une ressource correspond. Lorsque la ressource correspond, elle est considérée comme applicable et présente l’état **Conforme** ou **Non conforme**. Si le champ **type** ou **nom** est la seule propriété dans la définition de règle de stratégie, toutes les ressources sont considérées comme applicables et sont évaluées.

Le pourcentage de conformité est déterminé en divisant le nombre de ressources **conformes** par le _nombre total de ressources_.
Le _nombre total de ressources_ est défini comme étant la somme des ressources **conformes**, **non conformes** et **en conflit**. La conformité globale est la somme des ressources distinctes **conformes** divisée par la somme de toutes les ressources distinctes. Dans l’image ci-dessous, il y a 20 ressources distinctes applicables et une seule **non conforme**. La conformité globale des ressources est égale à 95 % (soit 19 sur 20).

![Exemple de conformité à la stratégie à partir de la page de conformité](../media/getting-compliance-data/simple-compliance.png)

## <a name="portal"></a>Portail

Le portail Azure permet de visualiser et comprendre l’état de conformité de votre environnement selon une représentation graphique. Sur la page **Stratégie**, l’option **Vue d’ensemble** fournit des détails sur les étendues disponibles pour la conformité des stratégies et des initiatives. En complément de l’état de conformité et du nombre par affectation, elle contient un graphique retraçant la conformité au cours des sept derniers jours.
La page **Conformité** regroupe essentiellement les mêmes informations (à l’exception du graphique), mais avec également des options de tri et de filtrage supplémentaires.

![Exemple de page de conformité à la stratégie](../media/getting-compliance-data/compliance-page.png)

Comme une stratégie ou une initiative peut être affectée à différentes étendues, le tableau comprend l’étendue pour chaque affectation et le type de définition qui a été affecté. Le nombre de ressources et de stratégies non conformes est aussi indiqué pour chaque affectation. En cliquant sur une stratégie ou une initiative dans le tableau, vous obtenez davantage de détails sur la conformité de l’affectation concernée.

![Exemple de page de détails de conformité de stratégie](../media/getting-compliance-data/compliance-details.png)

La liste des ressources dans l’onglet **Resource compliance (Conformité des ressources)** affiche l’état de l’évaluation des ressources existantes pour l’affectation actuelle. Par défaut, l’onglet est défini sur **Non conforme**, mais un filtre peut être appliqué.
Les événements (ajouter, effectuer un audit, refuser, déployer) déclenchés par la requête pour créer une ressource sont affichés dans l’onglet **Événements**.

![Exemple d’événements de la conformité à la stratégie](../media/getting-compliance-data/compliance-events.png)

Cliquez avec le bouton droit sur la ligne de l’événement pour lequel vous souhaitez obtenir plus de détails et sélectionnez **Afficher les journaux d’activité**. La page Journal d’activité s’ouvre et les critères de recherche sont préfiltrés pour montrer les détails de l’affectation et des événements. Le journal d’activité fournit davantage de contexte ainsi que des informations supplémentaires sur ces événements.

![Exemple de journal d’activité de conformité de stratégie](../media/getting-compliance-data/compliance-activitylog.png)

### <a name="understand-non-compliance"></a>Comprendre la non-conformité

<a name="change-history-preview"></a>

Lorsqu’une ressource est déterminé comme étant **non conformes**, il existe plusieurs raisons. Pour déterminer la cause est une ressource **non conformes** ou pour rechercher la responsable de la modification, consultez [déterminer une non-conformité](./determine-non-compliance.md).

## <a name="command-line"></a>Ligne de commande

Les informations disponibles dans le portail peuvent être récupérées à l’aide de l’API REST (y compris avec [ARMClient](https://github.com/projectkudu/ARMClient)) ou à l’aide d’Azure PowerShell. Pour plus d’informations sur l’API REST, consultez la référence [Policy Insights](/rest/api/policy-insights/). Les pages de référence de l’API REST contiennent, pour chaque opération, un bouton vert qui vous permet de la tester directement dans votre navigateur.

Pour utiliser les exemples suivants dans Azure PowerShell, créez un jeton d’authentification avec cet exemple de code. Remplacez ensuite le $restUri par la chaîne de votre choix dans les exemples pour récupérer un objet JSON pouvant être analysé.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

$azContext = Get-AzContext
$azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = New-Object -TypeName Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient -ArgumentList ($azProfile)
$token = $profileClient.AcquireAccessToken($azContext.Subscription.TenantId)
$authHeader = @{
    'Content-Type'='application/json'
    'Authorization'='Bearer ' + $token.AccessToken
}

# Define the REST API to communicate with
# Use double quotes for $restUri as some endpoints take strings passed in single quotes
$restUri = "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/summarize?api-version=2018-04-04"

# Invoke the REST API
$response = Invoke-RestMethod -Uri $restUri -Method POST -Headers $authHeader

# View the response object (as JSON)
$response
```

### <a name="summarize-results"></a>Synthétiser les résultats

Avec l’API REST, la synthèse peut être effectuée par conteneur, par définition ou par affectation. Voici un exemple de synthèse obtenu au niveau de l’abonnement à l’aide de la fonction de [résumé de l’abonnement](/rest/api/policy-insights/policystates/summarizeforsubscription) de Policy Insights :

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/summarize?api-version=2018-04-04
```

La sortie offre une synthèse de l’abonnement. Dans l’exemple de sortie ci-dessous, la conformité est synthétisée sous **value.results.nonCompliantResources** et **value.results.nonCompliantPolicies**. Cette requête fournit des détails supplémentaires, notamment sur chaque affectation concernée par la non-conformité, fournit des informations sur la définition de chaque affectation. Chaque objet de stratégie dans la hiérarchie fournit un **queryResultsUri** qui peut être utilisé pour obtenir des détails supplémentaires à ce niveau.

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#summary",
    "@odata.count": 1,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#summary/$entity",
        "results": {
            "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false",
            "nonCompliantResources": 15,
            "nonCompliantPolicies": 1
        },
        "policyAssignments": [{
            "policyAssignmentId": "/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77",
            "policySetDefinitionId": "",
            "results": {
                "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77'",
                "nonCompliantResources": 15,
                "nonCompliantPolicies": 1
            },
            "policyDefinitions": [{
                "policyDefinitionReferenceId": "",
                "policyDefinitionId": "/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "effect": "deny",
                "results": {
                    "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'",
                    "nonCompliantResources": 15
                }
            }]
        }]
    }]
}
```

### <a name="query-for-resources"></a>Interroger des ressources

En reprenant l’exemple ci-dessus, **value.policyAssignments.policyDefinitions.results.queryResultsUri** nous donne un exemple d’URI pour toutes les ressources non conformes pour une définition de stratégie spécifique. Si l’on observe la valeur **$filter**on remarque que IsCompliant est défini sur false, que PolicyAssignmentId est spécifié pour la définition de stratégie, suivi de PolicyDefinitionId. Le PolicyAssignmentId est inclus dans le filtre car le PolicyDefinitionId peut figurer dans plusieurs stratégies ou affectations initiatives avec une diversité d’étendues. En spécifiant à la fois le PolicyAssignmentId et le PolicyDefinitionId, nous pouvons être explicites dans les résultats que nous recherchons. Auparavant, nous utilisions la valeur **latest (dernières)** pour PolicyStates : celle-ci définit automatiquement les paramètres **from (depuis)** et **to (jusqu’à)** d’une plage de temps dans les dernières 24 heures.

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'
```

L’exemple de réponse ci-dessous a été ramené à une seule ressource non conforme par souci de concision. La réponse détaillée fournit plusieurs éléments de données sur la ressource, la stratégie ou initiative, et l’affectation. Notez que vous pouvez également voir quels paramètres d’affectation ont été transmis à la définition de la stratégie.

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
    "@odata.count": 15,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
        "timestamp": "2018-05-19T04:41:09Z",
        "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/rg-tags/providers/Microsoft.Compute/virtualMachines/linux",
        "policyAssignmentId": "/subscriptions/{subscriptionId}/resourceGroups/rg-tags/providers/Microsoft.Authorization/policyAssignments/37ce239ae4304622914f0c77",
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
        "effectiveParameters": "",
        "isCompliant": false,
        "subscriptionId": "{subscriptionId}",
        "resourceType": "/Microsoft.Compute/virtualMachines",
        "resourceLocation": "westus2",
        "resourceGroup": "RG-Tags",
        "resourceTags": "tbd",
        "policyAssignmentName": "37ce239ae4304622914f0c77",
        "policyAssignmentOwner": "tbd",
        "policyAssignmentParameters": "{\"tagName\":{\"value\":\"costCenter\"},\"tagValue\":{\"value\":\"Contoso-Test\"}}",
        "policyAssignmentScope": "/subscriptions/{subscriptionId}/resourceGroups/RG-Tags",
        "policyDefinitionName": "1e30110a-5ceb-460c-a204-c1c3969c6d62",
        "policyDefinitionAction": "deny",
        "policyDefinitionCategory": "tbd",
        "policySetDefinitionId": "",
        "policySetDefinitionName": "",
        "policySetDefinitionOwner": "",
        "policySetDefinitionCategory": "",
        "policySetDefinitionParameters": "",
        "managementGroupIds": "",
        "policyDefinitionReferenceId": ""
    }]
}
```

### <a name="view-events"></a>Visualiser les événements

Lorsqu’une ressource est créée ou mise à jour, un résultat d’évaluation de stratégie est généré. Ces résultats sont appelés _événements de stratégie_. L’URI suivant permet d’afficher les événements de stratégie récents associés à l’abonnement.

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/default/queryResults?api-version=2018-04-04
```

Vos résultats doivent ressembler à l’exemple suivant :

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/$metadata#default",
    "@odata.count": 1,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/$metadata#default/$entity",
        "NumAuditEvents": 16
    }]
}
```

Pour plus d’informations sur l’interrogation des événements de stratégie, consultez l’article de référence intitulé [Événements de stratégie](/rest/api/policy-insights/policyevents).

### <a name="azure-powershell"></a>Azure PowerShell

Le module Azure PowerShell de Policy est disponible dans PowerShell Gallery sous le nom [Az.PolicyInsights](https://www.powershellgallery.com/packages/Az.PolicyInsights). Vous pouvez utiliser PowerShellGet pour installer le module à l’aide de `Install-Module -Name Az.PolicyInsights` (vérifiez que vous disposez de la dernière version [d’Azure PowerShell](/powershell/azure/install-az-ps)) :

```azurepowershell-interactive
# Install from PowerShell Gallery via PowerShellGet
Install-Module -Name Az.PolicyInsights

# Import the downloaded module
Import-Module Az.PolicyInsights

# Login with Connect-AzAccount if not using Cloud Shell
Connect-AzAccount
```

Le module comporte les cmdlets suivantes :

- `Get-AzPolicyStateSummary`
- `Get-AzPolicyState`
- `Get-AzPolicyEvent`
- `Get-AzPolicyRemediation`
- `Remove-AzPolicyRemediation`
- `Start-AzPolicyRemediation`
- `Stop-AzPolicyRemediation`

Exemple : Obtenir le résumé d’état de la stratégie affectée au premier plan qui comporte le plus grand nombre de ressources non conformes.

```azurepowershell-interactive
PS> Get-AzPolicyStateSummary -Top 1

NonCompliantResources : 15
NonCompliantPolicies  : 1
PolicyAssignments     : {/subscriptions/{subscriptionId}/resourcegroups/RG-Tags/providers/micros
                        oft.authorization/policyassignments/37ce239ae4304622914f0c77}
```

Exemple : Obtenir l’enregistrement de l’état pour la ressource évaluée la plus récemment (par défaut, selon un horodatage dans l’ordre décroissant).

```azurepowershell-interactive
PS> Get-AzPolicyState -Top 1

Timestamp                  : 5/22/2018 3:47:34 PM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/networkInterfaces/linux316
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
IsCompliant                : False
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/networkInterfaces
ResourceLocation           : westus2
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
```

Exemple : Obtenir des détails pour toutes les ressources non conformes du réseau virtuel.

```azurepowershell-interactive
PS> Get-AzPolicyState -Filter "ResourceType eq '/Microsoft.Network/virtualNetworks'"

Timestamp                  : 5/22/2018 4:02:20 PM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/virtualNetworks/RG-Tags-vnet
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
IsCompliant                : False
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/virtualNetworks
ResourceLocation           : westus2
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
```

Exemple : Obtenir des événements liés aux ressources non conformes du réseau virtuel qui se sont produits après une date spécifique.

```azurepowershell-interactive
PS> Get-AzPolicyEvent -Filter "ResourceType eq '/Microsoft.Network/virtualNetworks'" -From '2018-05-19'

Timestamp                  : 5/19/2018 5:18:53 AM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/virtualNetworks/RG-Tags-vnet
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
IsCompliant                : False
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/virtualNetworks
ResourceLocation           : eastus
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
TenantId                   : {tenantId}
PrincipalOid               : {principalOid}
```

Le champ **PrincipalOid** peut être utilisé pour obtenir un utilisateur spécifique avec l’applet de commande `Get-AzADUser` d’Azure PowerShell. Remplacez **{principalOid}** par la réponse que vous obtenez à partir de l’exemple précédent.

```azurepowershell-interactive
PS> (Get-AzADUser -ObjectId {principalOid}).DisplayName
Trent Baker
```

## <a name="azure-monitor-logs"></a>Journaux d’activité Azure Monitor

Si vous avez un [espace de travail Analytique de journal](../../../log-analytics/log-analytics-overview.md) avec `AzureActivity` à partir de la [solution d’Analytique de journal d’activité](../../../azure-monitor/platform/collect-activity-logs.md) liée à votre abonnement, vous pouvez également afficher les résultats de non-conformité du cycle d’évaluation à l’aide requêtes Kusto simples et `AzureActivity` table. Grâce aux informations des journaux d’activité Azure Monitor, des alertes peuvent être configurées de manière à signaler les problèmes de non-conformité.

![Conformité aux stratégies à l’aide des journaux d’activité Azure Monitor](../media/getting-compliance-data/compliance-loganalytics.png)

## <a name="next-steps"></a>Étapes suivantes

- Consulter des exemples à la page [Exemples Azure Policy](../samples/index.md)
- Consulter la page [Structure de définition Azure Policy](../concepts/definition-structure.md)
- Consulter la page [Compréhension des effets d’Azure Policy](../concepts/effects.md)
- Savoir comment [créer des stratégies par programmation](programmatically-create.md)
- Découvrir comment [remédier à la non-conformité des ressources](remediate-resources.md)
- Pour en savoir plus sur les groupes d’administration, consultez [Organiser vos ressources avec des groupes d’administration Azure](../../management-groups/overview.md).