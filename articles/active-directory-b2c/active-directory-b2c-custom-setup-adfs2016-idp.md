---
title: Ajouter ADFS en tant que fournisseur d’identité SAML en utilisant les stratégies personnalisées dans Azure Active Directory B2C | Microsoft Docs
description: Configurer ADFS 2016 à l’aide du protocole SAML et de stratégies personnalisées dans Azure Active Directory B2C
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/07/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: df4acf72a1a6ea134e1192512fda1d8cf1e92f0a
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65767994"
---
# <a name="add-adfs-as-a-saml-identity-provider-using-custom-policies-in-azure-active-directory-b2c"></a>Ajouter ADFS en tant que fournisseur d’identités SAML à l’aide de stratégies personnalisées dans Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Cet article explique comment autoriser la connexion d’un compte d’utilisateur ADFS à l’aide de [stratégies personnalisées](active-directory-b2c-overview-custom.md) dans Azure Active Directory (Azure AD) B2C. Vous allez activer la connexion en ajoutant un [profil technique SAML](saml-technical-profile.md) à une stratégie personnalisée.

## <a name="prerequisites"></a>Conditions préalables

- Suivez les étapes de l’article [Prise en main des stratégies personnalisées dans Azure Active Directory B2C](active-directory-b2c-get-started-custom.md).
- Assurez-vous d’avoir accès à un fichier .pfx de certificat avec une clé privée. Vous pouvez générer votre propre certificat autosigné et le charger dans Azure AD B2C. Azure AD B2C utilise ce certificat pour signer la requête SAML envoyée à votre fournisseur d’identité SAML.

## <a name="create-a-policy-key"></a>Création d’une clé de stratégie

Vous devez enregistrer votre certificat dans votre client Azure AD B2C.

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).
2. Vérifiez que vous utilisez le répertoire qui contient votre locataire Azure AD B2C. Sélectionnez le **filtre Directory et abonnement** dans le menu supérieur et sélectionnez le répertoire qui contient votre client.
3. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Azure AD B2C**.
4. Dans la page de vue d’ensemble, sélectionnez **Infrastructure d’expérience d’identité**.
5. Sélectionnez **Clés de stratégie**, puis **Ajouter**.
6. Pour **Options**, choisissez `Upload`.
7. Entrez un **nom** pour la clé de stratégie. Par exemple : `SamlCert`. Le préfixe `B2C_1A_` est ajouté automatiquement au nom de votre clé.
8. Recherchez et sélectionnez votre fichier de certificat .pfx contenant la clé privée.
9. Cliquez sur **Créer**.

## <a name="add-a-claims-provider"></a>Ajout d’un fournisseur de revendications

Si vous souhaitez que les utilisateurs se connectent à l’aide d’un compte ADFS, vous devez définir le compte en tant que fournisseur de revendications avec lequel Azure AD B2C peut communiquer par le biais d’un point de terminaison. Le point de terminaison fournit un ensemble de revendications utilisées par Azure AD B2C pour vérifier qu’un utilisateur spécifique s’est authentifié. 

Vous pouvez définir un compte ADFS en tant que fournisseur de revendications en l’ajoutant à l’élément **ClaimsProviders** dans le fichier d’extension de votre stratégie.

1. Ouvrez le fichier *TrustFrameworkExtensions.xml*.
2. Recherchez l’élément **ClaimsProviders**. S’il n’existe pas, ajoutez-le sous l’élément racine.
3. Ajoutez un nouveau **ClaimsProvider** comme suit :

    ```xml
    <ClaimsProvider>
      <Domain>contoso.com</Domain>
      <DisplayName>Contoso ADFS</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Contoso-SAML2">
          <DisplayName>Contoso ADFS</DisplayName>
          <Description>Login with your ADFS account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="PartnerEntity">https://your-ADFS-domain/federationmetadata/2007-06/federationmetadata.xml</Item>
            <Item Key="XmlSignatureAlgorithm">Sha256</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_ADFSSamlCert"/>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_ADFSSamlCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="userPrincipalName" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name"/>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication"/>
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. Remplacez `your-ADFS-domain` par le nom de votre domaine ADFS et remplacez la valeur de la revendication de sortie **identityProvider** par votre DNS (valeur arbitraire qui indique votre domaine).
5. Enregistrez le fichier .

### <a name="upload-the-extension-file-for-verification"></a>Télécharger le fichier d’extension pour la vérification

À ce stade, vous avez configuré votre stratégie afin qu’Azure AD B2C sache comment communiquer avec votre compte ADFS. Essayez de télécharger le fichier d’extension de votre stratégie juste pour confirmer qu’il ne présente aucun problème pour le moment.

1. Sur la page **Stratégies personnalisées** dans votre locataire Azure AD B2C, sélectionnez **Charger une stratégie**.
2. Activez **Remplacer la stratégie si elle existe**, puis recherchez et sélectionnez le fichier *TrustFrameworkExtensions.xml*.
3. Cliquez sur **Télécharger**.

## <a name="register-the-claims-provider"></a>Inscription du fournisseur de revendications

À ce stade, le fournisseur d’identité a été configuré, mais il n’est disponible dans aucun des écrans d’inscription ou de connexion. Pour changer cela, vous pouvez créer un doublon d’un modèle de parcours utilisateur et le modifier afin qu’il dispose également du fournisseur d’identité ADFS.

1. Ouvrez le fichier *TrustFrameworkBase.xml* à partir du pack de démarrage.
2. Recherchez et copiez l’intégralité du contenu de l’élément **UserJourney** comprenant `Id="SignUpOrSignIn"`.
3. Ouvrez le fichier *TrustFrameworkExtensions.xml*, puis recherchez l’élément **UserJourneys**. Si l’élément n’existe pas, ajoutez-en un.
4. Collez l’intégralité du contenu de l’élément **UserJourney** que vous avez copié en tant qu’enfant de l’élément **UserJourneys**.
5. Renommez l’ID du parcours utilisateur. Par exemple : `SignUpSignInADFS`.

### <a name="display-the-button"></a>Afficher le bouton

L’élément **ClaimsProviderSelection** est analogue à un bouton de fournisseur d’identité sur un écran d’inscription ou de connexion. Si vous ajoutez un élément **ClaimsProviderSelection** à un compte ADFS, un nouveau bouton s’affiche quand un utilisateur arrive sur la page.

1. Recherchez l’élément **OrchestrationStep** comprenant `Order="1"` dans le parcours utilisateur que vous avez créé.
2. Sous **ClaimsProviderSelections**, ajoutez l’élément suivant. Définissez la valeur de l’élément **TargetClaimsExchangeId** sur une valeur appropriée, par exemple `ContosoExchange` :

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Lier le bouton à une action

Maintenant que vous avez un bouton en place, vous devez le lier à une action. L’action est, dans ce cas, la communication d’Azure AD B2C avec un compte ADFS pour recevoir un jeton.

1. Recherchez l’élément **OrchestrationStep** comprenant `Order="2"` dans le parcours utilisateur.
2. Ajoutez l’élément **ClaimsExchange** suivant en veillant à utiliser pour l’ID la même valeur que celle que vous avez utilisée pour **TargetClaimsExchangeId** :

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
    ```
    
    Mettez à jour la valeur de **TechnicalProfileReferenceId** sur l’ID du profil technique que vous avez créé. Par exemple : `Contoso-SAML2`.

3. Enregistrez le fichier *TrustFrameworkExtensions.xml* et rechargez-le à des fins de vérification.


## <a name="configure-an-adfs-relying-party-trust"></a>Configurer une approbation de partie de confiance ADFS

Pour utiliser ADFS comme fournisseur d’identité dans AAD B2C, vous devez créer une approbation de partie de confiance ADFS avec les métadonnées SAML d’AAD B2C. L’exemple suivant montre une adresse URL aux métadonnées SAML d’un profil technique d’AAD B2C :

```
https://login.microsoftonline.com/te/your-tenant/your-policy/samlp/metadata?idptp=your-technical-profile
```

Remplacez les valeurs suivantes :

- **your-tenant** par le nom de votre locataire, par exemple, your-tenant.onmicrosoft.com.
- **your-policy** par le nom de votre stratégie. Par exemple, B2C_1A_signup_signin_adfs.
- **votre profil de technique** par le nom de votre profil technique du fournisseur d’identité SAML. Par exemple, Contoso-SAML2.
 
Ouvrez un navigateur et accédez à l’URL. Veillez à taper l’URL est correcte pour avoir accès au fichier de métadonnées XML. Pour ajouter une nouvelle approbation de partie de confiance en utilisant le composant logiciel enfichable de gestion ADFS et configurer manuellement les paramètres, effectuez la procédure suivante sur un serveur de fédération. L’appartenance au groupe **Administrateurs** ou à un groupe équivalent sur l’ordinateur local est la condition minimale requise pour effectuer cette procédure.

1. Dans Gestionnaire de serveur, sélectionnez **Outils**, puis **Gestion ADFS**.
2. Sélectionnez **Ajouter une approbation de partie de confiance**.
3. Dans la page **Bienvenue**, choisissez **Prise en charge des revendications**, puis cliquez sur **Démarrer**.
4. Dans la page **Sélectionner une source de données**, sélectionnez **Importer des données sur la partie de confiance publiées en ligne ou sur un réseau local**, fournissez votre URL de métadonnées AAD B2C, puis cliquez sur **Suivant**.
5. Dans la page **Indiquer le nom complet**, entrez un **Nom d’affichage**. Sous **Notes**, entrez une description pour cette approbation de partie de confiance, puis cliquez sur **Suivant**.
6. Dans la page **Sélectionner une stratégie de contrôle d’accès**, choisissez une stratégie, puis cliquez sur **Suivant**.
7. Sur la page **Prêt à ajouter l’approbation**, passez en revue les paramètres, puis cliquez sur **Suivant** pour enregistrer vos informations d’approbation de partie de confiance.
8. Sur la page **Terminer**, cliquez sur **Fermer**, cette action affiche automatiquement la boîte de dialogue **Modifier les règles de revendication**.
9. Sélectionnez **Ajouter une règle**.  
10. Dans **Modèle de règle de revendication**, sélectionnez **Envoyer les attributs LDAP en tant que revendications**.
11. Fournissez un **Nom de règle de revendication**. Pour le **Magasin d’attributs** choisissez **Sélectionner Active Directory**, ajoutez les revendications suivantes, puis cliquez sur **Terminer** et **OK**.

    | Attribut LDAP | Type de revendication sortante |
    | -------------- | ------------------- |
    | User-Principal-Name | userPrincipalName |
    | Nom | family_name |
    | Given-Name | given_name |
    | E-Mail-Address | email |
    | Display-Name | name |
    
12.  Selon votre type de certificat, il se peut que vous deviez définir l’algorithme de hachage. Dans la fenêtre de propriétés de l’approbation de partie de confiance (Démo B2C), sélectionnez l’onglet **Avancé**, remplacez l’**Algorithme de hachage sécurisé** par `SHA-256`, puis cliquez sur **OK**.  
13. Dans Gestionnaire de serveur, sélectionnez **Outils**, puis **Gestion ADFS**.
14. Sélectionnez l’approbation de partie de confiance que vous avez créée, choisissez **Mettre à jour à partir des métadonnées de fédération**, puis cliquez sur **Mettre à jour**. 

## <a name="create-an-azure-ad-b2c-application"></a>Création d’une application Azure AD B2C

La communication avec Azure AD B2C s’effectue via une application que vous créez dans votre locataire. Cette section indique les étapes facultatives que vous pouvez effectuer pour créer une application de test si vous ne l’avez pas déjà fait.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Veillez à utiliser l’annuaire qui contient votre locataire Azure AD B2C en cliquant sur le **filtre Répertoire et abonnement** dans le menu du haut et en choisissant l’annuaire qui contient votre locataire.
3. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Azure AD B2C**.
4. Sélectionnez **Applications**, puis **Ajouter**.
5. Entrez un nom pour l’application (par exemple, *testapp1*).
6. Pour **Application/API web**, sélectionnez `Yes`, puis entrez `https://jwt.ms` pour l’**URL de réponse**.
7. Cliquez sur **Créer**.

### <a name="update-and-test-the-relying-party-file"></a>Mettre à jour et tester le fichier de partie de confiance

Mettez à jour le fichier de partie de confiance qui lance le parcours utilisateur que vous avez créé.

1. Faites une copie de *SignUpOrSignIn.xml* dans votre répertoire de travail, puis renommez-le. Par exemple, renommez-le *SignUpSignInADFS.xml*.
2. Ouvrez le nouveau fichier et définissez une valeur unique pour l’attribut **PolicyId** de **TrustFrameworkPolicy**. Par exemple : `SignUpSignInADFS`.
3. Mettez à jour la valeur de **PublicPolicyUri** avec l’URI de la stratégie. Exemple : `http://contoso.com/B2C_1A_signup_signin_adfs`
4. Définissez l’attribut **ReferenceId** dans **DefaultUserJourney** sur l’ID du parcours utilisateur que vous avez créé (SignUpSignInADFS).
5. Enregistrez vos modifications, chargez le fichier, puis sélectionnez la nouvelle stratégie dans la liste.
6. Vérifiez que l’application Azure AD B2C que vous avez créée est sélectionnée dans le champ **Sélectionner une application**, puis testez-la en cliquant sur **Exécuter maintenant**.

