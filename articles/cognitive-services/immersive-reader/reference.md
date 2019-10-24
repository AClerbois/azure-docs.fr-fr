---
title: Référence du kit de développement logiciel (SDK) Lecteur immersif
titleSuffix: Azure Cognitive Services
description: Référence correspondant au kit de développement logiciel (SDK) du lecteur immersif
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: 1908ed916d61c7a65b1f0061c0fe8d8a08b5e41c
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388109"
---
# <a name="immersive-reader-sdk-reference-guide"></a>Guide de référence du SDK du Lecteur immersif

Le kit de développement logiciel (SDK) du lecteur immersif est une bibliothèque JavaScript qui vous permet d’intégrer le lecteur immersif à votre application.

# <a name="functions"></a>Fonctions

Le SDK expose les fonctions suivantes :

- [`ImmersiveReader.launchAsync(token, subdomain, content, options)`](#launchasync)

- [`ImmersiveReader.close()`](#close)

- [`ImmersiveReader.renderButtons(options)`](#renderbuttons)

## <a name="launchasync"></a>launchAsync

Lance le lecteur immersif au sein d'un `iframe` de votre application web.

```typescript
launchAsync(token: string, subdomain: string, content: Content, options?: Options): Promise<HTMLDivElement>;
```

### <a name="parameters"></a>parameters

| Nom | type | Description |
| ---- | ---- |------------ |
| `token` | string | Le jeton d’authentification Azure AD. Consultez le [guide pratique sur l’authentification Azure AD](./azure-active-directory-authentication.md). |
| `subdomain` | string | Sous-domaine personnalisé de votre ressource Lecteur immersif dans Azure. Consultez le [guide pratique sur l’authentification Azure AD](./azure-active-directory-authentication.md). |
| `content` | [Contenu](#content) | Objet dans lequel figure le contenu à afficher dans le lecteur immersif. |
| `options` | [Options](#options) | Options de configuration de certains comportements du lecteur immersif. facultatif. |

### <a name="returns"></a>Retours

Retourne un `Promise<HTMLDivElement>` qui se résout quand le Lecteur immersif est chargé. `Promise` se résout en élément `div` dont le seul enfant correspond à un élément `iframe` contenant la page du lecteur immersif.

### <a name="exceptions"></a>Exceptions

L'élément `Promise` retourné est rejeté avec un objet [`Error`](#error) si le lecteur immersif ne se charge pas. Pour plus d’informations, consultez les [codes d’erreur](#error-codes).

## <a name="close"></a>close

Ferme le Lecteur immersif.

Par exemple, cette fonction est utile si ```hideExitButton: true``` est défini dans [options](#options) pour masquer le bouton Quitter. Un autre bouton (par exemple, la flèche retour d’un en-tête mobile) peut appeler cette fonction ```close``` quand l’utilisateur clique dessus.

```typescript
close(): void;
```

## <a name="renderbuttons"></a>renderButtons

Cette fonction définit un style et met à jour les boutons du Lecteur immersif du document. Si ```options.elements``` est spécifié, cette fonction affiche les boutons dans ```options.elements```. Sinon, les boutons sont affichés dans les éléments du document qui ont la classe ```immersive-reader-button```.

Cette fonction est appelée automatiquement par le SDK au moment du chargement de la fenêtre.

Pour plus d’options de rendu, consultez [Attributs optionnels](#optional-attributes).

```typescript
renderButtons(options?: RenderButtonsOptions): void;
```

### <a name="parameters"></a>parameters

| Nom | type | Description |
| ---- | ---- |------------ |
| `options` | [RenderButtonsOptions](#renderbuttonsoptions) | Options de configuration de certains comportements de la fonction renderButtons. facultatif. |

## <a name="types"></a>Types

### <a name="content"></a>Contenu

Contient le contenu à afficher dans le lecteur immersif.

```typescript
{
    title?: string;    // Title text shown at the top of the Immersive Reader (optional)
    chunks: Chunk[];   // Array of chunks
}
```

### <a name="chunk"></a>Bloc

Bloc de données unique qui est passé dans le contenu du Lecteur immersif.

```typescript
{
    content: string;        // Plain text string
    lang?: string;          // Language of the text, e.g. en, es-ES (optional). Language will be detected automatically if not specified.
    mimeType?: string;      // MIME type of the content (optional). Currently 'text/plain', 'application/mathml+xml', and 'text/html' are supported. Defaults to 'text/plain' if not specified.
}
```

#### <a name="supported-mime-types"></a>Types MIME pris en charge

| Type MIME | Description |
| --------- | ----------- |
| texte/brut | Texte brut. |
| texte/html | Contenu HTML. [En savoir plus](#html-support)|
| application/mathml+xml | Langage de balisage mathématique (MathML). [Plus d’informations](https://developer.mozilla.org/en-US/docs/Web/MathML)
| application/vnd.openxmlformats-officedocument.wordprocessingml.document | Document au format .docx Microsoft Word.

### <a name="html-support"></a>Prise en charge du langage HTML
| HTML | Contenu pris en charge |
| --------- | ----------- |
| Styles de police | Gras, Italique, Souligné, Code, Barré, Exposant, Indice |
| Listes non triées | Disque, Cercle, Carré |
| Listes triées | Décimal, Alphabet en majuscules, Alphabet en minuscules, Chiffres romains en majuscules, Chiffres romains en minuscules |
| Liens hypertexte | Bientôt disponible |

Les étiquettes non prises en charge sont affichées de manière comparable. Les images et les tables ne sont pas prises en charge pour l’instant.

### <a name="options"></a>Options

Contient les propriétés qui configurent certains comportements du lecteur immersif.

```typescript
{
    uiLang?: string;           // Language of the UI, e.g. en, es-ES (optional). Defaults to browser language if not specified.
    timeout?: number;          // Duration (in milliseconds) before launchAsync fails with a timeout error (default is 15000 ms).
    uiZIndex?: number;         // Z-index of the iframe that will be created (default is 1000)
    useWebview?: boolean;      // Use a webview tag instead of an iframe, for compatibility with Chrome Apps (default is false).
    onExit?: () => any;        // Executes when the Immersive Reader exits
    customDomain?: string;     // Reserved for internal use. Custom domain where the Immersive Reader webapp is hosted (default is null).
    allowFullscreen?: boolean; // The ability to toggle fullscreen (default is true).
    hideExitButton?: boolean;  // Whether or not to hide the Immersive Reader's exit button arrow (default is false). This should only be true if there is an alternative mechanism provided to exit the Immersive Reader (e.g a mobile toolbar's back arrow).
}
```

### <a name="renderbuttonsoptions"></a>RenderButtonsOptions

Options de rendu des boutons du Lecteur immersif.

```typescript
{
    elements: HTMLDivElement[];    // Elements to render the Immersive Reader buttons in
}
```

### <a name="error"></a>Error

Contient des informations sur l'erreur.

```typescript
{
    code: string;    // One of a set of error codes
    message: string; // Human-readable representation of the error
}
```

#### <a name="error-codes"></a>Codes d’erreur

| Code | Description |
| ---- | ----------- |
| BadArgument | L'argument fourni n’est pas valide. Pour plus d'informations, voir `message`. |
| Délai d'expiration | Le lecteur immersif n'a pas été chargé dans le délai spécifié. |
| TokenExpired | Le jeton fourni a expiré. |
| Throttled | Le taux d’appels maximal a été dépassé. |

## <a name="launching-the-immersive-reader"></a>Lancement du lecteur immersif

Le kit de développement logiciel (SDK) fournit le style par défaut du bouton de lancement du lecteur immersif. Utilisez l'attribut de classe `immersive-reader-button` pour activer ce style.

```html
<div class='immersive-reader-button'></div>
```

### <a name="optional-attributes"></a>Attributs facultatifs

Utilisez les attributs suivants pour configurer l’apparence du bouton.

| Attribut | Description |
| --------- | ----------- |
| `data-button-style` | Définit le style du bouton. Peut être `icon`, `text` ou `iconAndText`. La valeur par défaut est `icon`. |
| `data-locale` | Définit les paramètres régionaux. Par exemple, `en-US` ou `fr-FR`. La valeur par défaut est l’anglais (`en`). |
| `data-icon-px-size` | Définit la taille de l’icône en pixels. La valeur par défaut est 20px. |

## <a name="browser-support"></a>Prise en charge des navigateurs

Pour une meilleure expérience avec le lecteur immersif, utilisez les versions les plus récentes des navigateurs suivants.

* Microsoft Edge
* Internet Explorer 11
* Google Chrome
* Mozilla Firefox
* Apple Safari

## <a name="next-steps"></a>Étapes suivantes

* Explorer le [kit de développement logiciel (SDK) du lecteur immersif sur GitHub](https://github.com/microsoft/immersive-reader-sdk)
* [Démarrage rapide : Créer une application web qui lance le lecteur immersif (C#)](./quickstart.md)