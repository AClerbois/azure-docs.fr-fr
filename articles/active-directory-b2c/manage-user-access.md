---
title: Gérer les accès utilisateur dans Azure Active Directory B2C | Microsoft Docs
description: Découvrez comment identifier les mineurs, recueillir les données relatives à la date de naissance et au pays, et obtenir l’acceptation des conditions d’utilisation dans votre application à l’aide d’Azure AD B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/24/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 2fb9de7781fae51792e2802002a3cf68974dec83
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60383964"
---
# <a name="manage-user-access-in-azure-active-directory-b2c"></a>Gérer l’accès utilisateur dans Azure Active Directory B2C

Cet article explique comment gérer les accès utilisateur à vos applications à l’aide d’Azure Active Directory (Azure AD) B2C. La gestion de l’accès dans votre application inclut :

- Identification des mineurs et contrôle de l’accès utilisateur à votre application.
- Obligation d’obtenir le consentement parental pour que les mineurs utilisent vos applications.
- Collecte des données relatives à la date de naissance et au pays des utilisateurs.
- Capture de l’acceptation des conditions d’utilisation et de régulation de l’accès.

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

## <a name="control-minor-access"></a>Contrôle d’accès des mineurs

Les applications et les organisations peuvent décider d’empêcher les mineurs d’utiliser des applications et des services qui ne sont pas adaptés à ce public. Les applications et les organisations peuvent aussi décider d’accepter les mineurs, et par conséquent gérer le consentement parental et fournir des expériences autorisées aux mineurs comme stipulées par les règles d’entreprise et autorisées par la législation. 

Si un utilisateur est identifié comme mineur, vous pouvez définir le flux utilisateur dans Azure AD B2C selon l’une des trois options suivantes :

- **Renvoyer un jeton id_token JWT signé à l’application** : L’utilisateur est enregistré dans le répertoire, et un jeton est retourné à l’application. L’application continue à s’exécuter conformément aux règles d’entreprise. Par exemple, l’application peut lancer un processus de consentement parental. Pour utiliser cette méthode, choisissez de recevoir les revendications **ageGroup** et **consentProvidedForMinor** à partir de l’application.

- **Envoyer un jeton JSON non signé à l’application** : Azure AD B2C indique à l’application que l’utilisateur est mineur, et fournit l’état de consentement parental de l’utilisateur. L’application continue à s’exécuter conformément aux règles d’entreprise. Un jeton JSON ne permet pas d’effectuer une authentification réussie avec l’application. L’application doit traiter l’utilisateur non authentifié conformément aux revendications incluses dans le jeton JSON, qui peut inclure **name**, **email**, **ageGroup** et **consentProvidedForMinor**.

- **Bloquer l’utilisateur** : Si un utilisateur est mineur et que le consentement parental n’a pas été donné, Azure AD B2C peut avertir l’utilisateur qu’il ou elle est bloqué(e). Aucun jeton n’est émis, l’accès est bloqué et le compte d’utilisateur n’est pas créé au moment de l’inscription. Pour implémenter cette notification, vous fournissez une page de contenu HTML/CSS appropriée afin d’informer l’utilisateur et de présenter les options appropriées. Aucune action supplémentaire émanant de l’application n’est requise pour les nouvelles inscriptions.

## <a name="get-parental-consent"></a>Obtenir le consentement parental

Selon la législation relative à l’application, il peut être nécessaire d’obtenir le consentement parental auprès d’un utilisateur adulte vérifié. Azure AD B2C ne fournit pas d’expérience permettant de vérifier l’âge d’un individu, puis d’autoriser un adulte vérifié à donner un consentement parental à un mineur. Cette expérience doit être fournie par l’application ou un autre fournisseur de services.

Voici un exemple de flux utilisateur pour le recueil du consentement parental :

1. Une opération d’[API Graph Azure Active Directory](/previous-versions/azure/ad/graph/api/api-catalog) identifie l’utilisateur en tant que mineur et retourne les données utilisateur à l’application sous la forme d’un jeton JSON non signé.

2. L’application traite le jeton JSON et affiche un écran à l’intention du mineur pour l’informer de la nécessité du consentement parental et demander le consentement parental en ligne. 

3. Azure AD B2C affiche un parcours d’authentification que l’utilisateur peut suivre pour se connecter normalement, et émet un jeton à l’application qui doit inclure **legalAgeGroupClassification = “minorWithParentalConsent”**. L’application collecte l’adresse e-mail du parent et vérifie que ce dernier est un adulte. Pour ce faire, elle fait appel à une source de confiance, par exemple un bureau des cartes d’identité, la vérification du permis de conduire ou une preuve de possession d’une carte de crédit. Si la vérification réussit, l’application demande au mineur de se connecter à l’aide du flux utilisateur Azure AD B2C. Si le consentement est refusé (par exemple, si **legalAgeGroupClassification = "minorWithoutParentalConsent"**), Azure AD B2C retourne un jeton JSON (et non un compte de connexion) à destination de l’application afin de recommencer le processus de consentement. Il est possible de personnaliser le flux utilisateur de façon à ce qu’un mineur ou un adulte puisse récupérer l’accès au compte d’un mineur en envoyant un code d’inscription à l’adresse e-mail du mineur ou à l’adresse e-mail de l’adulte enregistré.

4. L’application permet au mineur de révoquer le consentement.

5. Lorsqu’un mineur ou un adulte révoque le consentement, l’API Graph Azure AD peut servir à modifier la valeur de **consentProvidedForMinor** afin d’afficher **denied**. L’application peut également supprimer un mineur dont le consentement a été révoqué. Il est possible de personnaliser le flux utilisateur de façon à ce que le mineur authentifié (ou le parent utilisant le compte du mineur) puisse révoquer le consentement. Azure AD B2C enregistre **consentProvidedForMinor** avec la valeur **denied**.

Pour plus d’informations sur **legalAgeGroupClassification**, **consentProvidedForMinor** et **ageGroup**, consultez [Type de ressource utilisateur](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/user). Pour plus d’informations sur les attributs personnalisés, consultez [Utiliser des attributs personnalisés pour recueillir des informations sur vos consommateurs](active-directory-b2c-reference-custom-attr.md). Lorsque vous traitez des attributs étendus à l’aide de l’API Graph Azure AD, vous devez utiliser la version longue de l’attribut, par exemple *extension_18b70cf9bb834edd8f38521c2583cd86_dateOfBirth* : *2011-01-01T00:00:00Z*.

## <a name="gather-date-of-birth-and-country-data"></a>Recueillir la date de naissance et le nom du pays

Les applications peuvent se servir d’Azure AD B2C pour recueillir la date de naissance et le pays de tous les utilisateurs au moment de l’inscription. Si ces informations ne sont pas déjà disponibles, l’application peut les demander à l’utilisateur lors de sa prochaine authentification (connexion). Les utilisateurs ne peuvent pas continuer sans fournir leur date de naissance et leur pays. Azure AD B2C utilise ces informations pour déterminer si l’utilisateur est considéré comme un mineur conformément à la législation du pays en question. 

Un flux utilisateur personnalisé peut recueillir les informations sur la date de naissance et le pays, puis utiliser la transformation des revendications Azure AD B2C pour déterminer **ageGroup** et conserver le résultat (ou conserver directement la date de naissance et le pays) dans le répertoire.

Les étapes suivantes illustrent la logique utilisée pour calculer **ageGroup** à partir de la date de naissance de l’utilisateur :

1. Essayez de rechercher le pays en fonction du code de pays dans la liste. Si vous ne trouvez pas le pays, revenez à la valeur **Default**.

2. Si le nœud **MinorConsent** est présent dans l’élément de pays :

    a. Calculez la date à laquelle l’utilisateur doit être né pour être considéré comme un adulte. Par exemple, si la date actuelle est le 14 mars 2015, et que **MinorConsent** est 18, la date de naissance doit être au plus tard le 14 mars 2000.

    b. Comparez la date de naissance minimale avec la date de naissance réelle. Si la date de naissance minimale se situe avant la date de naissance de l’utilisateur, le calcul renvoie **Minor** lors du calcul de la tranche d’âge.

3. Si le nœud **MinorNoConsentRequired** est présent dans l’élément de pays, répétez les étapes 2a et 2 b avec la valeur issue de **MinorNoConsentRequired**. La sortie de l’étape 2b retourne **MinorNoConsentRequired** si la date de naissance minimale se situe avant la date de naissance de l’utilisateur. 

4. Si aucun des deux calculs ne renvoie la valeur true, le calcul renvoie **Adult**.

Si une application a recueilli la date de naissance et le pays par d’autres méthodes fiables, elle peut utiliser l’API Graph pour mettre à jour l’enregistrement utilisateur avec ces informations. Par exemple : 

- Si un utilisateur est connu en tant qu’adulte, mettez à jour l’attribut **ageGroup** du répertoire avec la valeur **Adult**.
- Si un utilisateur est connu en tant que mineur, mettez à jour l’attribut de répertoire **ageGroup** avec la valeur **Minor** et définissez **consentProvidedForMinor**, selon le cas.

Pour plus d’informations sur la collecte des données de date de naissance, consultez [Utiliser la liaison en fonction de l’âge dans Azure AD B2C](basic-age-gating.md).

## <a name="capture-terms-of-use-agreement"></a>Capturer l’acceptation des conditions d’utilisation

Lorsque vous développez une application, vous capturez généralement l’acceptation des conditions d’utilisation par les utilisateurs au sein de leurs applications, sans aucune ou très peu de participation provenant du répertoire utilisateur. Cependant, il est possible d’utiliser un flux utilisateur Azure AD B2C pour recueillir l’acceptation des conditions d’utilisation par l’utilisateur, restreindre l’accès si l’accord n’a pas été obtenu, et appliquer l’acceptation des modifications futures pour les conditions d’utilisation en fonction de la date de dernière acceptation et de la date de dernière version des conditions d’utilisation.

Les **conditions d’utilisation** peuvent également inclure l’option « Consent to share data with third parties » (Accepter de partager des données avec des tiers). Selon les réglementations locales et les règles d’entreprise, vous pouvez collecter l’acceptation d’un utilisateur des deux conditions combinées, ou vous pouvez autoriser l’utilisateur à accepter une condition et pas l’autre.

Les étapes suivantes décrivent comment vous pouvez gérer les conditions d’utilisation :

1. Enregistrez l’acceptation des conditions d’utilisation et la date d’acceptation à l’aide de l’API Graph et des attributs étendus. Cette opération est possible avec les flux utilisateur intégrés et personnalisés. Nous vous recommandons de créer et d’utiliser les attributs **extension_termsOfUseConsentDateTime** et **extension_termsOfUseConsentVersion**.

2. Créez une case à cocher obligatoire intitulée « Accepter les conditions d’utilisation » et enregistrez le résultat pendant l’inscription. Cette opération est possible avec les flux utilisateur intégrés et personnalisés.

3. Azure AD B2C stocke les données d’acceptation liées aux conditions d’utilisation et l’acceptation de l’utilisateur. Vous pouvez utiliser l’API Graph pour interroger l’état d’un utilisateur en lisant l’attribut d’extension qui sert à enregistrer la réponse (par exemple en lisant **termsOfUseTestUpdateDateTime**). Cette opération est possible avec les flux utilisateur intégrés et personnalisés.

4. Exigez l’acceptation des conditions d’utilisation mises à jour en comparant la date d’acceptation avec la date de dernière version des conditions d’utilisation. Vous pouvez comparer les dates uniquement en utilisant un flux d’utilisateur personnalisé. Utilisez l’attribut étendu **extension_termsOfUseConsentDateTime** et comparez la valeur à la revendication de **termsOfUseTextUpdateDateTime**. Si l’acceptation est ancienne, forcez une nouvelle acceptation en affichant un écran déclaré automatiquement. Sinon, bloquez l’accès à l’aide de la logique de stratégie.

5. Exigez l’acceptation des conditions d’utilisation mises à jour en comparant le numéro de version de l’acceptation avec le numéro de la dernière version acceptée. Vous pouvez comparer les numéros de version uniquement en utilisant un flux d’utilisateur personnalisé. Utilisez l’attribut étendu **extension_termsOfUseConsentDateTime** et comparez la valeur à la revendication de **extension_termsOfUseConsentVersion**. Si l’acceptation est ancienne, forcez une nouvelle acceptation en affichant un écran déclaré automatiquement. Sinon, bloquez l’accès à l’aide de la logique de stratégie.

Vous pouvez capturer l’acceptation des conditions d’utilisation dans les scénarios suivants :

- Un nouvel utilisateur s’inscrit. Les conditions d’utilisation s’affichent et le résultat d’acceptation est stocké.
- Un utilisateur qui a déjà accepté les conditions d’utilisation actives ou les plus récentes se connecte. Les conditions d’utilisation ne s’affichent pas.
- Un utilisateur qui n’a pas encore accepté les conditions d’utilisation actives ou les conditions d’utilisation les plus récentes se connecte. Les conditions d’utilisation s’affichent et le résultat d’acceptation est stocké.
- Un utilisateur qui a déjà accepté une ancienne version des conditions d’utilisation, désormais mises à jour vers la dernière version, se connecte. Les conditions d’utilisation s’affichent et le résultat d’acceptation est stocké.

L’image suivante montre le flux utilisateur recommandé :

![Flux utilisateur d’acceptation](./media/manage-user-access/user-flow.png) 

Voici un exemple de consentement pour des conditions d’utilisation basées sur la date et l’heure dans une revendication :

```
<ClaimsTransformations>
  <ClaimsTransformation Id="GetNewUserAgreeToTermsOfUseConsentDateTime" TransformationMethod="GetCurrentDateTime">
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="extension_termsOfUseConsentDateTime" TransformationClaimType="currentDateTime" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="IsTermsOfUseConsentRequired" TransformationMethod="IsTermsOfUseConsentRequired">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="extension_termsOfUseConsentDateTime" TransformationClaimType="termsOfUseConsentDateTime" />
    </InputClaims>
    <InputParameters>
      <InputParameter Id="termsOfUseTextUpdateDateTime" DataType="dateTime" Value="2098-01-30T23:03:45" />
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="termsOfUseConsentRequired" TransformationClaimType="result" />
    </OutputClaims>
  </ClaimsTransformation>
</ClaimsTransformations>
```

Voici un exemple de consentement pour des conditions d’utilisation basées sur la version dans une revendication :

```
<ClaimsTransformations>
  <ClaimsTransformation Id="GetEmptyTermsOfUseConsentVersionForNewUser" TransformationMethod="CreateStringClaim">
    <InputParameters>
      <InputParameter Id="value" DataType="string" Value=""/>
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="extension_termsOfUseConsentVersion" TransformationClaimType="createdClaim" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="GetNewUserAgreeToTermsOfUseConsentVersion" TransformationMethod="CreateStringClaim">
    <InputParameters>
      <InputParameter Id="value" DataType="string" Value="V1"/>
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="extension_termsOfUseConsentVersion" TransformationClaimType="createdClaim" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="IsTermsOfUseConsentRequiredForVersion" TransformationMethod="CompareClaimToValue">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="extension_termsOfUseConsentVersion" TransformationClaimType="inputClaim1" />
    </InputClaims>
    <InputParameters>
      <InputParameter Id="compareTo" DataType="string" Value="V1" />
      <InputParameter Id="operator" DataType="string" Value="not equal" />
      <InputParameter Id="ignoreCase" DataType="string" Value="true" />
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="termsOfUseConsentRequired" TransformationClaimType="outputClaim" />
    </OutputClaims>
  </ClaimsTransformation>
</ClaimsTransformations> 
```

## <a name="next-steps"></a>Étapes suivantes

- Pour savoir comment supprimer et exporter des données utilisateur, consultez [Gérer les données utilisateur](manage-user-data.md).
