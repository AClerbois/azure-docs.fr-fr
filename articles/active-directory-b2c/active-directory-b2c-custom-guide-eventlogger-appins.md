---
title: Suivre le comportement des utilisateurs à l’aide d’événements dans Application Insights à partir d’Azure Active Directory B2C | Microsoft Docs
description: Découvrez comment activer les journaux d’événements dans Application Insights à partir des parcours utilisateur Azure AD B2C à l’aide de stratégies personnalisées (préversion).
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 10/12/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: c02757fb4b48ebf1220a5826bc9699741faa5170
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71066184"
---
# <a name="track-user-behavior-in-azure-active-directory-b2c-using-application-insights"></a>Suivre le comportement des utilisateurs dans Azure Active Directory B2C à l’aide d’Application Insights

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Quand vous utilisez Azure Active Directory B2C (Azure AD B2C) avec Azure Application Insights, vous pouvez obtenir des journaux des événements détaillés et personnalisés pour vos parcours utilisateur. Dans cet article, vous apprendrez comment :

* Obtenir des informations détaillées sur le comportement des utilisateurs
* Résoudre les problèmes de vos propres stratégies en développement ou en production
* Mesurer les performances
* Créer des notifications à partir d’Application Insights

## <a name="how-it-works"></a>Fonctionnement

L’infrastructure d’expérience d’identité d’Azure AD B2C inclut le fournisseur `Handler="Web.TPEngine.Providers.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0`. Il envoie des données d’événement directement à Application Insights à l’aide de la clé d’instrumentation fournie à Azure AD B2C.

Un profil technique utilise ce fournisseur pour définir un événement à partir d’Azure AD B2C. Le profil spécifie le nom de l’événement, les revendications qui sont enregistrées et la clé d’instrumentation. Pour publier un événement, le profil technique est ensuite ajouté en tant qu’élément `orchestration step` ou `validation technical profile` dans un parcours utilisateur personnalisé.

Application Insights peut unifier les événements en utilisant un ID de corrélation pour enregistrer une session utilisateur. Application Insights rend la session et les événements disponibles en quelques secondes et présente de nombreux outils d’analyse, d’exportation et de visualisation.

## <a name="prerequisites"></a>Prérequis

Suivez les étapes décrites dans [Bien démarrer avec les stratégies personnalisées dans Azure Active Directory B2C](active-directory-b2c-get-started-custom.md). Cet article part du principe que vous utilisez le pack de démarrage de stratégie personnalisée. Cependant, le pack de démarrage n’est pas obligatoire.

## <a name="create-an-application-insights-resource"></a>Création d’une ressource Application Insights dans Azure

Quand vous utilisez Application Insights avec Azure AD B2C, vous devez uniquement créer une ressource et obtenir la clé d’instrumentation.

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).
2. Veillez à utiliser l’annuaire qui contient votre abonnement Azure en sélectionnant le filtre **Annuaire et abonnement** dans le menu supérieur et en choisissant l’annuaire qui contient votre abonnement. Ce locataire n’est pas votre locataire Azure AD B2C.
3. Choisissez **Créer une ressource** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Application Insights**.
4. Cliquez sur **Créer**.
5. Entrez un **Nom** pour la ressource.
6. Pour **Type d’application**, sélectionnez **Application web ASP.NET**.
7. Pour **Groupe de ressources**, sélectionnez un groupe existant ou entrez un nom pour un nouveau groupe.
8. Cliquez sur **Créer**.
4. Une fois la ressource Application Insights créée, ouvrez-la, développez **Éléments principaux**, puis copiez la clé d’instrumentation.

![Vue d’ensemble d’Application Insights et clé d’instrumentation](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-insights.png)

## <a name="add-new-claimtype-definitions"></a>Ajouter de nouvelles définitions ClaimType

Ouvrez le fichier *TrustFrameworkExtensions.xml* à partir du pack de démarrage, puis ajoutez les éléments suivants à l’élément [BuildingBlocks](buildingblocks.md) :

```xml
<ClaimsSchema>
  <ClaimType Id="EventType">
    <DisplayName>EventType</DisplayName>
    <DataType>string</DataType>
    <AdminHelpText />
    <UserHelpText />
  </ClaimType>
  <ClaimType Id="PolicyId">
    <DisplayName>PolicyId</DisplayName>
    <DataType>string</DataType>
    <AdminHelpText />
    <UserHelpText />
  </ClaimType>
  <ClaimType Id="Culture">
    <DisplayName>Culture</DisplayName>
    <DataType>string</DataType>
    <AdminHelpText />
    <UserHelpText />
  </ClaimType>
  <ClaimType Id="CorrelationId">
    <DisplayName>CorrelationId</DisplayName>
    <DataType>string</DataType>
    <AdminHelpText />
    <UserHelpText />
  </ClaimType>
  <!--Additional claims used for passing claims to Application Insights Provider -->
  <ClaimType Id="federatedUser">
    <DisplayName>federatedUser</DisplayName>
    <DataType>boolean</DataType>
    <UserHelpText />
  </ClaimType>
  <ClaimType Id="parsedDomain">
    <DisplayName>Parsed Domain</DisplayName>
    <DataType>string</DataType>
    <UserHelpText>The domain portion of the email address.</UserHelpText>
  </ClaimType>
  <ClaimType Id="userInLocalDirectory">
    <DisplayName>userInLocalDirectory</DisplayName>
    <DataType>boolean</DataType>
    <UserHelpText />
  </ClaimType>
</ClaimsSchema>
```

## <a name="add-new-technical-profiles"></a>Ajouter de nouveaux profils techniques

Les profils techniques peuvent être considérés comme des fonctions dans l’infrastructure d’expérience d’identité d’Azure AD B2C. Ce tableau définit les profils techniques qui servent à ouvrir une session et à publier des événements.

| Profil technique | Tâche |
| ----------------- | -----|
| AzureInsights-Common | Crée un ensemble commun de paramètres à inclure dans tous les profils techniques Azure Insights. |
| AzureInsights-SignInRequest | Crée un événement SignIn avec un ensemble de revendications quand une demande de connexion a été reçue. |
| AzureInsights-UserSignup | Crée un événement UserSignup quand l’utilisateur déclenche l’option d’inscription dans un parcours d’inscription/de connexion. |
| AzureInsights-SignInComplete | Enregistre la réussite d’une authentification quand un jeton a été envoyé à l’application par partie de confiance. |

Ajoutez les profils au fichier *TrustFrameworkExtensions.xml* à partir du pack de démarrage. Ajoutez ces éléments à l’élément **ClaimsProviders** :

```xml
<ClaimsProvider>
  <DisplayName>Application Insights</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AzureInsights-SignInRequest">
      <InputClaims>
        <!-- An input claim with a PartnerClaimType="eventName" is required. This is used by the AzureApplicationInsightsProvider to create an event with the specified value. -->
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInRequest" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AzureInsights-Common" />
    </TechnicalProfile>
    <TechnicalProfile Id="AzureInsights-SignInComplete">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInComplete" />
        <InputClaim ClaimTypeReferenceId="federatedUser" PartnerClaimType="{property:FederatedUser}" DefaultValue="false" />
        <InputClaim ClaimTypeReferenceId="parsedDomain" PartnerClaimType="{property:FederationPartner}" DefaultValue="Not Applicable" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AzureInsights-Common" />
    </TechnicalProfile>
    <TechnicalProfile Id="AzureInsights-UserSignup">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="UserSignup" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AzureInsights-Common" />
    </TechnicalProfile>
    <TechnicalProfile Id="AzureInsights-Common">
      <DisplayName>Alternate Email</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <!-- The ApplicationInsights instrumentation key which will be used for logging the events -->
        <Item Key="InstrumentationKey">xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</Item>
        <!-- A Boolean that indicates whether developer mode is enabled. This controls how events are buffered. In a development environment with minimal event volume, enabling developer mode results in events being sent immediately to ApplicationInsights. -->
        <Item Key="DeveloperMode">false</Item>
        <!-- A Boolean that indicates whether telemetry should be enabled or not. -->
        <Item Key="DisableTelemetry ">false</Item>
      </Metadata>
      <InputClaims>
        <!-- Properties of an event are added through the syntax {property:NAME}, where NAME is property being added to the event. DefaultValue can be either a static value or a value that's resolved by one of the supported DefaultClaimResolvers. -->
        <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
        <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:JourneyId}" />
        <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
      </InputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

> [!IMPORTANT]
> Modifiez la clé d’instrumentation dans le profil technique `AzureInsights-Common` et remplacez-le par le GUID fourni par votre ressource Application Insights.

## <a name="add-the-technical-profiles-as-orchestration-steps"></a>Ajouter les profils techniques en tant qu’étapes d’orchestration

Appelez `Azure-Insights-SignInRequest` en tant qu’étape d’orchestration 2 pour suivre une demande de connexion/d’inscription reçue :

```xml
<!-- Track that we have received a sign in request -->
<OrchestrationStep Order="1" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AzureInsights-SignInRequest" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Immédiatement *avant* l’étape d’orchestration `SendClaims`, ajoutez une nouvelle étape qui appelle `Azure-Insights-UserSignup`. Celle-ci est déclenchée quand l’utilisateur sélectionne le bouton d’inscription dans un parcours d’inscription/connexion.

```xml
<!-- Handles the user clicking the sign up link in the local account sign in page -->
<OrchestrationStep Order="8" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
      <Value>newUser</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
    <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
      <Value>newUser</Value>
      <Value>false</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackUserSignUp" TechnicalProfileReferenceId="AzureInsights-UserSignup" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Immédiatement après l’étape d’orchestration `SendClaims`, appelez `Azure-Insights-SignInComplete`. Cette étape montre la réussite d’un parcours.

```xml
<!-- Track that we have successfully sent a token -->
<OrchestrationStep Order="10" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AzureInsights-SignInComplete" />
  </ClaimsExchanges>
</OrchestrationStep>
```

> [!IMPORTANT]
> Après avoir ajouté les nouvelles étapes d’orchestration, renumérotez les étapes séquentiellement sans sauter d’entiers de 1 à N.


## <a name="upload-your-file-run-the-policy-and-view-events"></a>Charger votre fichier, exécuter la stratégie et afficher les événements

Enregistrez et chargez le fichier *TrustFrameworkExtensions.xml*. Ensuite, appelez la stratégie de partie de confiance à partir de votre application ou utilisez l’option **Exécuter maintenant** dans le portail Azure. Quelques secondes plus tard, vos événements sont disponibles dans Application Insights.

1. Ouvrez la ressource **Application Insights** dans votre locataire Azure Active Directory.
2. Sélectionnez **Utilisation** > **Événements**.
3. Définissez **Pendant** sur **Dernière heure** et **Par** sur **3 minutes**.  Vous devrez peut-être sélectionner **Actualiser** pour afficher les résultats.

![Panneau d’événements - UTILISATION Application Insights](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-ins-graphic.png)

## <a name="next-steps"></a>Étapes suivantes

Ajoutez des types de revendication et des événements à votre parcours utilisateur en fonction de vos besoins. Vous pouvez utiliser des [résolveurs de revendication](claim-resolver-overview.md) ou n’importe quel type de revendication sous forme de chaîne, ou ajouter les revendications en ajoutant un élément **InputClaim** à l’événement Application Insights ou au profil technique AzureInsights-Common.

- **ClaimTypeReferenceId** est la référence à un type de revendication.
- **PartnerClaimType** est le nom de la propriété qui s’affiche dans Azure Insights. Utilisez la syntaxe `{property:NAME}`, où `NAME` est la propriété qui est ajoutée à l’événement.
- **DefaultValue** est n’importe quelle valeur de chaîne ou le résolveur de revendication.

```XML
<InputClaim ClaimTypeReferenceId="app_session" PartnerClaimType="{property:app_session}" DefaultValue="{OAUTH-KV:app_session}" />
<InputClaim ClaimTypeReferenceId="loyalty_number" PartnerClaimType="{property:loyalty_number}" DefaultValue="{OAUTH-KV:loyalty_number}" />
<InputClaim ClaimTypeReferenceId="language" PartnerClaimType="{property:language}" DefaultValue="{Culture:RFC5646}" />
```

