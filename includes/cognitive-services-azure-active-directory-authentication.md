---
author: erhopf
ms.author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 07/23/2019
ms.openlocfilehash: 3a6807cc204a5f8a6957bb03cf4dcbaf3611c17c
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/19/2019
ms.locfileid: "71148409"
---
## <a name="authenticate-with-azure-active-directory"></a>S’authentifier à l’aide d’Azure Active Directory

> [!IMPORTANT]
> Actuellement, **seuls** l’API Vision par ordinateur, l’API Visage, l’API Analyse de texte et le Lecteur immersif prennent en charge l’authentification avec Azure Active Directory (AAD).

Dans les sections précédentes, nous vous avons montré comment vous authentifier auprès d’Azure Cognitive Services en utilisant une clé d’abonnement monoservice ou multiservice. Bien que ces clés offrent un moyen simple et rapide de démarrer le développement, elles ne conviennent plus dans des scénarios plus complexes qui nécessitent des contrôles d’accès basés sur les rôles. Voyons ce qui est nécessaire pour s’authentifier avec Azure Active Directory (AAD).

Dans les sections suivantes, vous allez utiliser l’environnement Azure Cloud Shell ou Azure CLI pour créer un sous-domaine, affecter des rôles et obtenir un jeton de porteur pour appeler Azure Cognitive Services. Si vous êtes bloqué, des liens sont fournis dans chaque section avec toutes les options disponibles pour chaque commande dans Azure Cloud Shell/Azure CLI.

### <a name="create-a-resource-with-a-custom-subdomain"></a>Créer une ressource avec un sous-domaine personnalisé

La première étape consiste à créer un sous-domaine personnalisé.

1. Commencez par ouvrir Azure Cloud Shell. Ensuite, [sélectionnez un abonnement](https://docs.microsoft.com/powershell/module/servicemanagement/azure/select-azuresubscription?view=azuresmps-4.0.0#description) :

   ```azurecli-interactive
   Select-AzureSubscription -SubscriptionName <YOUR_SUBCRIPTION>
   ```

2. Ensuite, [créez une ressource Cognitive Services](https://docs.microsoft.com/powershell/module/az.cognitiveservices/new-azcognitiveservicesaccount?view=azps-1.8.0) avec un sous-domaine personnalisé. Le nom de sous-domaine doit être globalement unique et ne peut pas inclure de caractères spéciaux, comme : « . », « ! », « , ».

   ```azurecli-interactive
   New-AzCognitiveServicesAccount -ResourceGroupName <RESOURCE_GROUP_NAME> -name <ACCOUNT_NAME> -Type <ACCOUNT_TYPE> -SkuName <SUBSCRIPTION_TYPE> -Location <REGION> -CustomSubdomainName <UNIQUE_SUBDOMAIN>
   ```

3. Si l’opération réussit, le **point de terminaison** doit afficher le nom du sous-domaine unique pour votre ressource.


### <a name="assign-a-role-to-a-service-principal"></a>Attribuer un rôle à un principal de service

Maintenant que vous disposez d’un sous-domaine personnalisé associé à votre ressource, vous devez affecter un rôle à un principal du service.

> [!NOTE]
> Gardez à l’esprit que les affectations de rôles AAD peuvent prendre jusqu’à cinq minutes pour se propager.

1. Nous allons d’abord inscrire une [application AAD](https://docs.microsoft.com/powershell/module/Az.Resources/New-AzADApplication?view=azps-1.8.0).

   ```azurecli-interactive
   $SecureStringPassword = ConvertTo-SecureString -String <YOUR_PASSWORD> -AsPlainText -Force

   New-AzADApplication -DisplayName <APP_DISPLAY_NAME> -IdentifierUris <APP_URIS> -Password $SecureStringPassword
   ```

   Vous aurez besoin de l’**ApplicationId** à l’étape suivante.

2. Ensuite, vous devez [créer un principal du service](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal?view=azps-1.8.0) pour l’application AAD.

   ```azurecli-interactive
   New-AzADServicePrincipal -ApplicationId <APPLICATION_ID>
   ```

   >[!NOTE]
   > Si vous inscrivez une application dans le portail Azure, cette étape est effectuée pour vous.

3. La dernière étape consiste à [affecter le rôle « Utilisateur Cognitive Services »](https://docs.microsoft.com/powershell/module/az.Resources/New-azRoleAssignment?view=azps-1.8.0) au principal du service (délimité à la ressource). En affectant un rôle, vous accordez au principal du service l’accès à cette ressource. Vous pouvez accorder au même principal du service l’accès à plusieurs ressources de votre abonnement.
   >[!NOTE]
   > L’ObjectId du principal du service est utilisé, et non pas l’ObjectId de l’application.
   > ACCOUNT_ID est l’ID de ressource Azure du compte Cognitive Services que vous avez créé. Cet ID est indiqué dans les « propriétés » de la ressource dans le portail Azure.

   ```azurecli-interactive
   New-AzRoleAssignment -ObjectId <SERVICE_PRINCIPAL_OBJECTID> -Scope <ACCOUNT_ID> -RoleDefinitionName "Cognitive Services User"
   ```

### <a name="sample-request"></a>Exemple de requête

Dans cet exemple, un mot de passe est utilisé pour authentifier le principal du service. Le jeton fourni est ensuite utilisé pour appeler l’API Vision par ordinateur.

1. Obtenez votre **TenantId** :
   ```azurecli-interactive
   $context=Get-AzContext
   $context.Tenant.Id
   ```

2. Obtenez un jeton :
   ```azurecli-interactive
   $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList "https://login.windows.net/<TENANT_ID>"
   $secureSecretObject = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.SecureClientSecret" -ArgumentList $SecureStringPassword   
   $clientCredential = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential" -ArgumentList $app.ApplicationId, $secureSecretObject
   $token=$authContext.AcquireTokenAsync("https://cognitiveservices.azure.com/", $clientCredential).Result
   $token
   ```
3. Appelez l’API Vision par ordinateur :
   ```azurecli-interactive
   $url = $account.Endpoint+"vision/v1.0/models"
   $result = Invoke-RestMethod -Uri $url  -Method Get -Headers @{"Authorization"=$token.CreateAuthorizationHeader()} -Verbose
   $result | ConvertTo-Json
   ```

Le principal du service peut également être authentifié avec un certificat. En plus du principal du service, l’utilisateur principal est également pris en charge en ayant des autorisations déléguées via une autre application AAD. Dans ce cas, au lieu de mots de passe ou de certificats, les utilisateurs sont invités à fournir une authentification à deux facteurs lors de l’acquisition du jeton.
