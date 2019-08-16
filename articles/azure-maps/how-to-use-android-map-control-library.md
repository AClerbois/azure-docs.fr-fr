---
title: Bien démarrer avec le contrôle de carte Android dans Azure Maps | Microsoft Docs
description: Le contrôle de carte Android dans Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: a3423635ab226693e0b3b057e2c2cb441861ea1b
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68839411"
---
# <a name="getting-started-with-azure-maps-android-sdk"></a>Bien démarrer avec Android SDK Azure Maps

Android SDK Azure Maps est une bibliothèque de cartes de vecteur pour Android. Cet article vous guide tout au long des processus d’installation d’Android SDK Azure Maps et de chargement d’une carte.

## <a name="prerequisites"></a>Prérequis

### <a name="create-an-azure-maps-account"></a>Créer un compte Azure Maps

Pour accomplir les procédures de cet article, vous devez d’abord [créer un compte Azure Maps](how-to-manage-account-keys.md) dans le niveau tarifaire S1.

### <a name="download-android-studio"></a>Télécharger Android Studio

Avant d'installer Android SDK Azure Maps, téléchargez Android Studio et créez un projet avec une activité vide. Vous pouvez [télécharger Android Studio](https://developer.android.com/studio/) gratuitement sur Google. 

## <a name="create-a-project-in-android-studio"></a>Créer un projet dans Android Studio

Commencez par créer un projet avec une activité vide. Suivez ces étapes pour créer un projet Android Studio :

1. Sous **Choose your project** (Choisissez votre projet), sélectionnez **Phone and Tablet** (Téléphone et tablette). Votre application s’exécutera sur ce facteur de forme.
2. Dans l’onglet **Phone and Tablet** (Téléphone et tablette), sélectionnez **Empty Activity** (Activité vide), puis sélectionnez **Next** (Suivant).
3. Sous **Configurer votre projet**, sélectionnez `API 21: Android 5.0.0 (Lollipop)` comme SDK minimum. Il s’agit de la version minimale prise en charge par Android SDK Azure Maps.
4. Acceptez les valeurs par défaut `Activity Name` et `Layout Name`, puis cliquez sur **Finish** (Terminer).

Pour obtenir de l’aide supplémentaire sur l’installation d’Android Studio et la création d’un projet, consultez la [documentation Android Studio](https://developer.android.com/studio/intro/).

![Création d’un projet](./media/how-to-use-android-map-control-library/form-factor-android.png)

## <a name="set-up-a-virtual-device"></a>Configurer un appareil virtuel

Android Studio vous permet de configurer un appareil Android virtuel sur votre ordinateur. Cela peut vous aider à tester votre application pendant le développement. Pour configurer un appareil virtuel, sélectionnez l’icône du gestionnaire d’appareil virtuel Android (AVD, Android Virtual Device) dans le coin supérieur droit de votre écran de projet, puis sélectionnez **Create Virtual Device** (Créer un appareil virtuel). Vous pouvez également accéder au gestionnaire AVD en sélectionnant **Tools (Outils)**  > **Android** > **AVD Manager (Gestionnaire AVD)** à partir de la barre d’outils. Dans la catégorie **Phones** (Téléphones), sélectionnez **Nexus 5X** et cliquez sur **Next** (Suivant).

Pour en savoir plus sur la configuration d’un appareil virtuel Android, consultez la [documentation Android Studio](https://developer.android.com/studio/run/managing-avds).

![Émulateur Android](./media/how-to-use-android-map-control-library/android-emulator.png)

## <a name="install-the-azure-maps-android-sdk"></a>Installer Android SDK Azure Maps

L’étape suivante de la création de votre application consiste à installer Android SDK Azure Maps. Pour installer le Kit de développement logiciel (SDK), procédez comme suit :

1. Ouvrez le fichier **build.gradle** de niveau supérieur et ajoutez le code suivant à la section de bloc **all projects** (tous les projets), **repositories** (référentiels) :

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Modifiez l’élément **app/build.gradle** et ajoutez-y le code suivant :
    
    1. Assurez-vous que la version **minSdkVersion** de votre projet est l’API 21 ou une version ultérieure.

    2. Ajoutez le code suivant à la section Android :

        ```
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    3. Mettez à jour votre bloc de dépendances et ajoutez une nouvelle ligne de dépendance d’implémentation pour la dernière version d’Android SDK Azure Maps :

        ```
        implementation "com.microsoft.azure.maps:mapcontrol:0.2"
        ```

    > [!Note]
    > Android SDK Azure Maps est régulièrement mis à niveau et amélioré. Reportez-vous à la documentation [Bien démarrer avec le contrôle de carte Android](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) pour obtenir le dernier numéro de version d’implémentation d’Azure Maps. Vous pouvez également définir le numéro de version de « 0.2 » à « 0+ » afin qu’il pointe toujours vers la dernière version.

3. Modifiez **res** > **layout** > **activity_main.xml** et remplacez-le par le code :
    
    ```XML
    <?xml version="1.0" encoding="utf-8"?>
    <FrameLayout
        xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        >

        <com.microsoft.azure.maps.mapcontrol.MapControl
            android:id="@+id/mapcontrol"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            />
    </FrameLayout>
    ```

4. Dans le fichier **MainActivity.java**, vous devez :
    
    * Ajouter des importations pour le Kit de développement logiciel (SDK) Azure Maps
    * Définir vos informations d’authentification Azure Maps
    * Obtenir l’instance de contrôle de carte dans la méthode **onCreate**

    Si vous définissez les informations d’authentification sur la classe AzureMaps de façon globale à l’aide des méthodes setSubscriptionKey ou setAadProperties, vous n’aurez pas besoin d’ajouter vos informations d’authentification à chaque affichage. Le contrôle de carte contient ses propres méthodes de cycle de vie pour la gestion du cycle de vie OpenGL d’Android. Elles doivent être appelées directement à partir de l’activité contenante. Pour que votre application appelle correctement les méthodes de cycle de vie du contrôle de carte, vous devez remplacer les méthodes de cycle de vie suivantes dans l'activité qui contient le contrôle de carte et appeler la méthode de contrôle de carte correspondante. 

    Modifiez le fichier **MainActivity.java** comme suit :
    
    ```java
    package com.example.myapplication;

    import android.support.v7.app.AppCompatActivity;
    import android.os.Bundle;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import com.microsoft.azure.maps.mapcontrol.layer.SymbolLayer;
    import com.microsoft.azure.maps.mapcontrol.options.MapStyle;
    import com.microsoft.azure.maps.mapcontrol.source.DataSource;

    public class MainActivity extends AppCompatActivity {
        
        static {
            AzureMaps.setSubscriptionKey("<Your Azure Maps subscription key>");
        }

        MapControl mapControl;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);

            mapControl = findViewById(R.id.mapcontrol);

            mapControl.onCreate(savedInstanceState);

        }

        @Override
        public void onResume() {
            super.onResume();
            mapControl.onResume();
        }

        @Override
        public void onPause() {
            super.onPause();
            mapControl.onPause();
        }

        @Override
        public void onStop() {
            super.onStop();
            mapControl.onStop();
        }

        @Override
        public void onLowMemory() {
            super.onLowMemory();
            mapControl.onLowMemory();
        }

        @Override
        protected void onDestroy() {
            super.onDestroy();
            mapControl.onDestroy();
        }

        @Override
        protected void onSaveInstanceState(Bundle outState) {
            super.onSaveInstanceState(outState);
            mapControl.onSaveInstanceState(outState);
        }

    }

    ```

## <a name="import-classes"></a>Importer des classes

Après avoir accompli les étapes précédentes, vous obtiendrez probablement des avertissements d’Android Studio concernant certaines parties de code. Pour résoudre ces avertissements, importez les classes référencées dans `MainActivity.java`.

Vous pouvez importer automatiquement ces classes en appuyant sur Alt + Entrée (Option + Retour sur un Mac).

Sélectionnez le bouton d’exécution comme illustré dans la figure suivante (ou appuyez sur CTRL + R sur un Mac) pour générer votre application.

![Cliquez sur Exécuter.](./media/how-to-use-android-map-control-library/run-app.png)

La création de l’application par Android Studio prend quelques secondes. Une fois la création terminée, vous pouvez tester votre application dans l’appareil Android émulé. La carte doit ressembler à ceci :

<center>

![Carte Android](./media/how-to-use-android-map-control-library/android-map.png)</center>

## <a name="localizing-the-map"></a>Localisation de la carte

Android SDK Azure Maps propose trois méthodes différentes pour définir la langue et l'affichage régional de la carte. Le code suivant montre comment définir la langue sur le français (« fr-FR ») et l'affichage régional sur « auto ». 

La première option consiste à transférer la langue et à afficher les informations régionales dans la classe `AzureMaps` en utilisant les méthodes statiques `setLanguage` et `setView` globalement. Cela permet de définir la langue et l'affichage régional par défaut pour tous les contrôles Azure Maps chargés dans votre application.

```Java
static {
    //Set your Azure Maps Key.
    AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");

    //Set the language to be used by Azure Maps.
    AzureMaps.setLanguage("fr-FR");

    //Set the regional view to be used by Azure Maps.
    AzureMaps.setView("auto");
}
```

La deuxième option consiste à transférer la langue et à afficher les informations dans le code XML du contrôle de carte.

```XML
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_language="fr-FR"
    app:mapcontrol_view="auto"
    />
```

La troisième option consiste à définir la langue et l'affichage régional de la carte par programmation à l'aide de la méthode `setStyle`. Cette opération peut être effectuée à tout moment pour changer la langue et l'affichage régional de la carte.

```Java
mapControl.onReady(map -> {
    map.setStyle(StyleOptions.language("fr-FR"));
    map.setStyle(StyleOptions.view("auto"));
});
```

Voici un exemple Azure Maps dans lequel la langue est définie sur « fr-FR » et l'affichage régional sur « auto ».

<center>

![Représentation d'une carte avec des étiquettes en français](./media/how-to-use-android-map-control-library/android-localization.png)
</center>

La liste complète des langues et des affichages régionaux pris en charge est disponible [ici](supported-languages.md).

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment ajouter des données superposées sur la carte :

> [!div class="nextstepaction"]
> [Add a symbol layer to an Android map](https://review.docs.microsoft.com/azure/azure-maps/how-to-add-symbol-to-android-map) (Ajouter une couche de symboles à une carte Android)

> [!div class="nextstepaction"]
> [Add shapes to an Android map](https://docs.microsoft.com/azure/azure-maps/how-to-add-shapes-to-android-map) (Ajouter des formes à une carte Android)

> [!div class="nextstepaction"]
> [Change map styles in Android maps](https://docs.microsoft.com/azure/azure-maps/set-android-map-styles) (Modifier les styles de carte dans les cartes Android)
