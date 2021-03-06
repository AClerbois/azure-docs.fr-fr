---
title: Azure Red Hat OpenShift exécutant OpenShift 4 - Configurer l’authentification Azure Active Directory à l’aide de la ligne de commande
description: Découvrez comment configurer l’authentification Azure Active Directory pour un cluster Azure Red Hat OpenShift exécutant OpenShift 4 à l’aide de la ligne de commande
ms.service: container-service
ms.topic: article
ms.date: 03/12/2020
author: sabbour
ms.author: asabbour
keywords: aro, openshift, az aro, red hat, cli
ms.custom: mvc
ms.openlocfilehash: fd6ea0749cce154ae20479bc54ef9b7374a69d0c
ms.sourcegitcommit: 4feb198becb7a6ff9e6b42be9185e07539022f17
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2020
ms.locfileid: "89469420"
---
# <a name="configure-azure-active-directory-authentication-for-an-azure-red-hat-openshift-4-cluster-cli"></a>Configurer l’authentification Azure Active Directory pour un cluster Azure Red Hat OpenShift 4 (CLI)

Si vous choisissez d’installer et d’utiliser l’interface CLI localement, cet article vous demande d’exécuter Azure CLI version 2.6.0 ou ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

Récupérez vos URL spécifiques au cluster qui seront utilisées pour configurer l’application Azure Active Directory.

Construisez l’URL de rappel OAuth du cluster et stockez-la dans une variable **oauthCallbackURL**. Veillez à remplacer **aro-rg** par le nom de votre groupe de ressources et **aro-cluster** par le nom de votre cluster.

> [!NOTE]
> La section `AAD` de l’URL de rappel OAuth doit correspondre au nom du fournisseur d’identité OAuth que vous allez configurer ultérieurement.

```azurecli-interactive
domain=$(az aro show -g aro-rg -n aro-cluster --query clusterProfile.domain -o tsv)
location=$(az aro show -g aro-rg -n aro-cluster --query location -o tsv)
apiServer=$(az aro show -g aro-rg -n aro-cluster --query apiserverProfile.url -o tsv)
webConsole=$(az aro show -g aro-rg -n aro-cluster --query consoleProfile.url -o tsv)
oauthCallbackURL=https://oauth-openshift.apps.$domain.$location.aroapp.io/oauth2callback/AAD
```

## <a name="create-an-azure-active-directory-application-for-authentication"></a>Créez une application Azure Active Directory pour l’authentification

Créez une application Azure Active Directory et récupérez l’identificateur d’application créé. Remplacez **\<ClientSecret>** par un mot de passe sécurisé.

```azurecli-interactive
az ad app create \
  --query appId -o tsv \
  --display-name aro-auth \
  --reply-urls $oauthCallbackURL \
  --password '<ClientSecret>'
```

Vous devriez revenir à ce qui suit. Notez-le car il s’agit de la valeur **AppId** dont vous aurez besoin lors des étapes ultérieures.

```output
6a4cb4b2-f102-4125-b5f5-9ad6689f7224
```

Récupérez l’ID de locataire de l’abonnement qui possède l’application.

```azure
az account show --query tenantId -o tsv
```

Vous devriez revenir à ce qui suit. Notez qu’il s’agit du **TenantId** dont vous aurez besoin dans les étapes ultérieures.

```output
72f999sx-8sk1-8snc-js82-2d7cj902db47
```

## <a name="create-a-manifest-file-to-define-the-optional-claims-to-include-in-the-id-token"></a>Créer un fichier manifeste pour définir les revendications facultatives à inclure dans le jeton d’ID

Les développeurs d’applications peuvent utiliser des [revendications facultatives](../active-directory/develop/active-directory-optional-claims.md) dans leurs applications Azure AD pour spécifier les revendications souhaitées dans les jetons envoyés à leur application.

Vous pouvez utiliser des revendications facultatives pour :

- Sélectionner des revendications supplémentaires à inclure dans les jetons pour votre application.
- Modifier le comportement de certaines revendications retournées par Azure AD dans les jetons.
- Ajouter et accéder à des revendications personnalisées pour votre application.

Nous allons configurer OpenShift pour qu’il utilise la revendication `email` et revenir à `upn` pour définir le nom d’utilisateur par défaut en ajoutant le `upn` dans le cadre du jeton d’ID renvoyé par Azure Active Directory.

Créez un fichier **manifest.json** pour configurer l’application Azure Active Directory.

```bash
cat > manifest.json<< EOF
[{
  "name": "upn",
  "source": null,
  "essential": false,
  "additionalProperties": []
},
{
"name": "email",
  "source": null,
  "essential": false,
  "additionalProperties": []
}]
EOF
```

## <a name="update-the-azure-active-directory-applications-optionalclaims-with-a-manifest"></a>Mettre à jour les revendications facultatives de l’application Azure Active Directory avec un manifeste

Remplacez **\<AppID>** par l’ID que vous avez obtenu plus tôt.

```azurecli-interactive
az ad app update \
  --set optionalClaims.idToken=@manifest.json \
  --id <AppId>
```

## <a name="update-the-azure-active-directory-application-scope-permissions"></a>Mettre à jour les autorisations d’étendue d’application Azure Active Directory

Pour pouvoir lire les informations utilisateur d’Azure Active Directory, nous devons définir les étendues appropriées.

Remplacez **\<AppID>** par l’ID que vous avez obtenu plus tôt.

Pour activer la connexion et lire le profil utilisateur, ajoutez l’autorisation pour l’étendue **Azure Active Directory Graph.User.Read**.

```azurecli-interactive
az ad app permission add \
 --api 00000002-0000-0000-c000-000000000000 \
 --api-permissions 311a71cc-e848-46a1-bdf8-97ff7156d8e6=Scope \
 --id <AppId>
```

> [!NOTE]
> À moins que vous ne soyez authentifié en tant qu’administrateur général pour cette instance d’Azure Active Directory, vous pouvez ignorer le message demandant d’accorder le consentement, car vous êtes invité à le faire une fois que vous êtes connecté à votre propre compte.

## <a name="assign-users-and-groups-to-the-cluster-optional"></a>Affecter des utilisateurs et des groupes au cluster (facultatif)

Les applications inscrites dans un locataire Azure Active Directory (Azure AD) sont, par défaut, disponibles pour tous les utilisateurs du locataire qui parviennent à s’authentifier. Azure AD permet aux développeurs et administrateurs de locataires de limiter une application à un ensemble spécifique d’utilisateurs ou de groupes de sécurité dans le locataire.

Suivez les instructions de la documentation d’Azure Active Directory pour [affecter des utilisateurs et des groupes à l’application](../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md#app-registration).

## <a name="configure-openshift-openid-authentication"></a>Configurer l’authentification OpenShift OpenID

Récupérez les informations d’identification de `kubeadmin`. Exécutez la commande suivante pour rechercher le mot de passe de l’utilisateur `kubeadmin`.

```azurecli-interactive
az aro list-credentials \
  --name aro-cluster \
  --resource-group aro-rg
```

L’exemple de sortie suivant indique que le mot de passe sera dans `kubeadminPassword`.

```json
{
  "kubeadminPassword": "<generated password>",
  "kubeadminUsername": "kubeadmin"
}
```

Connectez-vous au serveur d’API du cluster OpenShift à l’aide de la commande suivante. La variable `$apiServer` a été définie [plus tôt](). Remplacez **\<kubeadmin password>** par le mot de passe que vous avez récupéré.

```azurecli-interactive
oc login $apiServer -u kubeadmin -p <kubeadmin password>
```

Créez un secret OpenShift pour stocker le secret de l’application Azure Active Directory, en remplaçant **\<ClientSecret>** par le secret que vous avez récupéré précédemment.

```azurecli-interactive
oc create secret generic openid-client-secret-azuread \
  --namespace openshift-config \
  --from-literal=clientSecret=<ClientSecret>
```

Créez un fichier **oidc.yaml** pour configurer l’authentification OpenShift OpenID sur Azure Active Directory. Remplacez **\<AppID>** et **\<TenantId>** par les valeurs que vous avez récupérées précédemment.

```bash
cat > oidc.yaml<< EOF
apiVersion: config.openshift.io/v1
kind: OAuth
metadata:
  name: cluster
spec:
  identityProviders:
  - name: AAD
    mappingMethod: claim
    type: OpenID
    openID:
      clientID: <AppId>
      clientSecret:
        name: openid-client-secret-azuread
      extraScopes:
      - email
      - profile
      extraAuthorizeParameters:
        include_granted_scopes: "true"
      claims:
        preferredUsername:
        - email
        - upn
        name:
        - name
        email:
        - email
      issuer: https://login.microsoftonline.com/<TenantId>
EOF
```

Appliquez la configuration au cluster.

```azurecli-interactive
oc apply -f oidc.yaml
```

Vous obtiendrez une réponse similaire à ce qui suit.

```output
oauth.config.openshift.io/cluster configured
```

## <a name="verify-login-through-azure-active-directory"></a>Vérifier la connexion via Azure Active Directory

Si vous vous déconnectez maintenant de la console web OpenShift et que vous tentez de vous reconnecter, une nouvelle option s’affiche pour vous permettre de vous connecter avec **AAD**. Vous devrez peut-être patienter quelques minutes.

![Écran de connexion avec l’option Azure Active Directory](media/aro4-login-2.png)