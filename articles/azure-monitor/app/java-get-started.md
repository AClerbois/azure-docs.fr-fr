---
title: Analyse d’une application web Java avec Azure Application Insights | Microsoft Docs
description: 'Analyse des performances des applications pour les applications web Java à l’aide d’Application Insights. '
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 05/24/2019
ms.openlocfilehash: 28fbb5fcfba2b346d0519dec79e538b1e513b7dd
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72817127"
---
# <a name="get-started-with-application-insights-in-a-java-web-project"></a>Prise en main d'Application Insights dans un projet web Java

[Application Insights](https://azure.microsoft.com/services/application-insights/) est un service d’analyse extensible pour développeurs web qui vous permet de comprendre les performances et l’utilisation de votre application en direct. Utilisez-le pour [instrumenter automatiquement des requêtes, effectuer le suivi des dépendances et recueillir les compteurs de performances](auto-collect-dependencies.md#java), diagnostiquer les problèmes de performances et les exceptions, et [écrire du code][api] pour suivre l’utilisation de votre application par les utilisateurs. 

![Capture d’écran des exemples de données de vue d’ensemble](./media/java-get-started/overview-graphs.png)

Application Insights prend en charge les applications Java exécutées sur Linux, Unix ou Windows.

Ce dont vous avez besoin :

* Java 7 ou ultérieur
* Un abonnement à [Microsoft Azure](https://azure.microsoft.com/).

## <a name="1-get-an-application-insights-instrumentation-key"></a>1. Obtenir une clé d'instrumentation Application Insights
1. Connectez-vous au [portail Microsoft Azure](https://portal.azure.com).
2. Créez une ressource Application Insights. Définissez le type d’application sur Application web Java.

3. Obtenez la clé d'instrumentation de la nouvelle ressource. Vous devrez la coller rapidement dans le code de votre projet.

    ![Dans la nouvelle vue d'ensemble des ressources, cliquez sur Propriétés et copiez la clé d'instrumentation.](./media/java-get-started/instrumentation-key-001.png)

## <a name="2-add-the-application-insights-sdk-for-java-to-your-project"></a>2. Ajoutez le Kit de développement logiciel (SDK) Application Insights pour Java à votre projet
*Choisissez la méthode adaptée à votre projet.*

#### <a name="if-youre-using-maven-a-namemaven-setup-"></a>Si vous utilisez Maven... <a name="maven-setup" />
Si votre projet est déjà configuré pour être assemblé avec Maven, fusionnez le code suivant dans votre fichier pom.xml.

Actualisez ensuite les dépendances du projet pour télécharger les fichiers binaires.

```XML
    <dependencies>
      <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>applicationinsights-web-auto</artifactId>
        <!-- or applicationinsights-web for manual web filter registration -->
        <!-- or applicationinsights-core for bare API -->
        <version>2.5.0</version>
      </dependency>
    </dependencies>
```

#### <a name="if-youre-using-gradle-a-namegradle-setup-"></a>Si vous utilisez Gradle... <a name="gradle-setup" />
Si votre projet est déjà configuré pour être assemblé avec Gradle, fusionnez le code suivant dans votre fichier build.gradle.xml.

Actualisez ensuite les dépendances du projet pour télécharger les fichiers binaires.

```gradle
    dependencies {
      compile group: 'com.microsoft.azure', name: 'applicationinsights-web-auto', version: '2.5.0'
      // or applicationinsights-web for manual web filter registration
      // or applicationinsights-core for bare API
    }
```

#### <a name="otherwise-if-you-are-manually-managing-dependencies-"></a>Sinon, si vous gérez manuellement les dépendances...
Téléchargez la [version la plus récente](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest) et copiez les fichiers nécessaires dans votre projet, en remplaçant toutes les versions antérieures.

### <a name="questions"></a>Questions...
* *Quelle est la relation entre les composants `-web-auto`, `-web` et `-core` ?*
  * `applicationinsights-web-auto` fournit des métriques qui effectuent le suivi du nombre de demandes servlet HTTP et des temps de réponse, en inscrivant automatiquement le filtre servlet Application Insights au moment de l’exécution.
  * `applicationinsights-web` fournit également des métriques qui effectuent le suivi du nombre de demandes HTTP servlet et des temps de réponse, mais nécessite une inscription manuelle du filtre de servlet Application Insights dans votre application.
  * `applicationinsights-core` vous offre uniquement l’API seule, par exemple, si votre application n’est pas basée sur un servlet.
  
* *Comment dois-je mettre à jour le Kit de développement logiciel (SDK) vers la dernière version ?*
  * Si vous utilisez Gradle ou Maven...
    * Mettez à jour votre fichier de build pour spécifier la version la plus récente.
  * Si vous gérez manuellement les dépendances...
    * Téléchargez le dernier [Kit de développement logiciel Application Insights pour Java](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest) et remplacez les anciens Kits. Les modifications sont décrites dans le [notes de publication du kit de développement logiciel (SDK)](https://github.com/Microsoft/ApplicationInsights-Java#release-notes).

## <a name="3-add-an-applicationinsightsxml-file"></a>3. Ajouter un fichier ApplicationInsights.xml
Ajoutez ApplicationInsights.xml dans le dossier de ressources de votre projet, ou vérifiez qu’il est ajouté au chemin de la classe du déploiement de votre projet. Copiez-y le code XML suivant.

Remplacez la clé d'instrumentation que avez obtenue sur le portail Azure.

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">

   <!-- The key from the portal: -->
   <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>

   <!-- HTTP request component (not required for bare API) -->
   <TelemetryModules>
      <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
   </TelemetryModules>

   <!-- Events correlation (not required for bare API) -->
   <!-- These initializers add context data to each event -->
   <TelemetryInitializers>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>
   </TelemetryInitializers>

</ApplicationInsights>
```

Si vous le souhaitez, le fichier config peut être hébergé dans n’importe quel emplacement accessible à votre application.  La propriété système `-Dapplicationinsights.configurationDirectory` spécifie le répertoire qui contient le fichier ApplicationInsights.xml. Par exemple, un fichier config situé à l’emplacement `E:\myconfigs\appinsights\ApplicationInsights.xml` doit être configuré avec la propriété `-Dapplicationinsights.configurationDirectory="E:\myconfigs\appinsights"`.

* La clé d'instrumentation est envoyée avec chaque élément de télémétrie et indique à Application Insights de l'afficher dans votre ressource.
* Le composant de demande HTTP est facultatif. Il envoie automatiquement la télémétrie concernant les demandes et les temps de réponse au portail.
* La corrélation des événements est un complément au composant de requête HTTP. Il assigne un identificateur à chaque demande reçue par le serveur et l'ajoute comme propriété de chaque élément de télémétrie en tant que propriété « Operation.Id ». Il vous permet de mettre en corrélation la télémétrie associée à chaque demande en définissant un filtre dans [recherche de diagnostic][diagnostic].

### <a name="alternative-ways-to-set-the-instrumentation-key"></a>Autres méthodes pour définir la clé d’instrumentation
Le kit de développement logiciel (SDK) d’Application Insights recherche la clé dans cet ordre :

1. Propriété système : -DAPPINSIGHTS_INSTRUMENTATIONKEY=your_ikey
2. Variable d’environnement : APPINSIGHTS_INSTRUMENTATIONKEY
3. Configuration de l'application : ApplicationInsights.xml

Vous pouvez également [définir la clé dans le code](../../azure-monitor/app/api-custom-events-metrics.md#ikey):

```java
    String instrumentationKey = "00000000-0000-0000-0000-000000000000";

    if (instrumentationKey != null)
    {
        TelemetryConfiguration.getActive().setInstrumentationKey(instrumentationKey);
    }
```

## <a name="4-add-agent"></a>4. Ajouter un agent

[Installez l’agent Java](java-agent.md) pour capturer les appels HTTP sortants, les requêtes JDBC, la journalisation des applications et une meilleure appelation des opérations.

## <a name="5-run-your-application"></a>5. Exécuter votre application
Exécutez-le en mode débogage sur votre ordinateur de développement, ou publiez-le sur votre serveur.

## <a name="6-view-your-telemetry-in-application-insights"></a>6. Voir votre télémétrie dans Application Insights
Revenez à votre ressource Application Insights sur le [portail Microsoft Azure](https://portal.azure.com).

Les données des demandes HTTP apparaissent dans le panneau Vue d’ensemble. (Si elles n’y sont pas, attendez quelques secondes et cliquez sur Actualiser).

![Capture d’écran des exemples de données de vue d’ensemble](./media/java-get-started/overview-graphs.png)

[En savoir plus sur les mesures.][metrics]

Cliquez sur un des graphiques pour afficher des métriques agrégées plus détaillées.

![Volet Échecs d’Application Insights avec des graphiques](./media/java-get-started/006-barcharts.png)

<!--
[TODO update image with 2.5.0 operation naming provided by agent]
-->

### <a name="instance-data"></a>Données d’instance
Cliquez sur un type de demande spécifique pour afficher les instances individuelles.

![Explorer un exemple de vue spécifique](./media/java-get-started/007-instance.png)

### <a name="analytics-powerful-query-language"></a>Analytics : Tirez parti d’un puissant langage de requête.
En accumulant toujours plus de données, vous pouvez exécuter des requêtes à la fois pour agréger les données et pour rechercher des instances individuelles.  [Analytics](../../azure-monitor/app/analytics.md) est un outil puissant qui permet non seulement de comprendre les performances et l’utilisation, mais également d’effectuer des diagnostics.

![Exemple d’Analytics](./media/java-get-started/0025.png)

## <a name="7-install-your-app-on-the-server"></a>7. Installer votre application sur le serveur
Publiez maintenant votre application sur le serveur, laissez le temps aux usagers de l’utiliser, puis observez les données de télémétrie qui s’affichent sur le portail.

* Assurez-vous que votre pare-feu autorise votre application à envoyer les données de télémétrie vers ces ports :

  * dc.services.VisualStudio.com:443
  * f5.services.visualstudio.com:443

* Si le trafic sortant doit être acheminé via un pare-feu, définissez les propriétés système `http.proxyHost` et `http.proxyPort`.

* Sur les serveurs Windows, installez :

  * [Redistribuable Microsoft Visual C++](https://www.microsoft.com/download/details.aspx?id=40784)

    (Cette opération active les compteurs de performances.)

## <a name="azure-app-service-config-spring-boot"></a>Configuration d’Azure App Service (Spring Boot)

Les applications Spring Boot qui s’exécutent sur Windows nécessitent une configuration supplémentaire pour s’exécuter sur Azure App Services. Modifiez le fichier **web.config** et ajoutez l’élément suivant :

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <system.webServer>
        <handlers>
            <add name="httpPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified"/>
        </handlers>
        <httpPlatform processPath="%JAVA_HOME%\bin\java.exe" arguments="-Djava.net.preferIPv4Stack=true -Dserver.port=%HTTP_PLATFORM_PORT% -jar &quot;%HOME%\site\wwwroot\AzureWebAppExample-0.0.1-SNAPSHOT.jar&quot;">
        </httpPlatform>
    </system.webServer>
</configuration>
```

## <a name="exceptions-and-request-failures"></a>Exceptions et échecs de requêtes
Les exceptions non prises en charge et les échecs de demande sont collectés automatiquement par le filtre web Application Insights.

Pour collecter des données sur d’autres exceptions, vous pouvez [insérer des appels à trackException() dans votre code][apiexceptions].

## <a name="monitor-method-calls-and-external-dependencies"></a>Surveiller les appels de méthode et les dépendances externes
[Installez l’agent Java](java-agent.md) pour journaliser les méthodes internes spécifiées et les appels effectués via JDBC, avec des données de minutage.

Et pour l’appellation automatique des opérations.

## <a name="w3c-distributed-tracing"></a>Traçage distribué W3C

Le kit de développement logiciel (SDK) Java Application Insights prend désormais en charge le [traçage distribué W3C](https://w3c.github.io/trace-context/).

La configuration entrante du SDK est expliquée plus en détail dans notre article consacré à la [corrélation](correlation.md#telemetry-correlation-in-the-java-sdk).

La configuration sortante du SDK est définie dans le fichier [AI-Agent.xml](java-agent.md).

## <a name="performance-counters"></a>Compteurs de performances
Ouvrez **Examiner**, **Métriques** afin d’afficher un ensemble de compteurs de performances.

![Capture d’écran du volet Métriques avec les octets privés du processus sélectionnés](./media/java-get-started/011-perf-counters.png)

### <a name="customize-performance-counter-collection"></a>Personnaliser la collecte des compteurs de performances
Pour désactiver la collecte du jeu standard de compteurs de performances, ajoutez le code suivant sous le nœud racine du fichier ApplicationInsights.xml :

```XML
    <PerformanceCounters>
       <UseBuiltIn>False</UseBuiltIn>
    </PerformanceCounters>
```

### <a name="collect-additional-performance-counters"></a>Collecter des compteurs de performances supplémentaires
Vous pouvez spécifier d'autres compteurs de performances à collecter.

#### <a name="jmx-counters-exposed-by-the-java-virtual-machine"></a>Compteurs JMX (exposés par la machine virtuelle Java)

```XML
    <PerformanceCounters>
      <Jmx>
        <Add objectName="java.lang:type=ClassLoading" attribute="TotalLoadedClassCount" displayName="Loaded Class Count"/>
        <Add objectName="java.lang:type=Memory" attribute="HeapMemoryUsage.used" displayName="Heap Memory Usage-used" type="composite"/>
      </Jmx>
    </PerformanceCounters>
```

* `displayName` : nom affiché sur le portail Application Insights.
* `objectName` : nom de l'objet JMX.
* `attribute` attribut du nom d'objet JMX à récupérer
* `type` (facultatif) : type d'attribut d'objet JMX :
  * Par défaut : un type simple, comme int ou long.
  * `composite`: les données du compteur de performances sont au format « Attribute.Data »
  * `tabular`: les données du compteur de performances sont au format ligne de tableau

#### <a name="windows-performance-counters"></a>Compteurs de performances Windows
Chaque [compteur de performances Windows](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx) est un membre d'une catégorie (de la même façon qu'un champ est un membre d'une classe). Les catégories peuvent être globales ou peuvent avoir des instances numérotées ou nommées.

```XML
    <PerformanceCounters>
      <Windows>
        <Add displayName="Process User Time" categoryName="Process" counterName="%User Time" instanceName="__SELF__" />
        <Add displayName="Bytes Printed per Second" categoryName="Print Queue" counterName="Bytes Printed/sec" instanceName="Fax" />
      </Windows>
    </PerformanceCounters>
```

* displayName : nom affiché sur le portail Application Insights.
* categoryName : catégorie du compteur de performances (objet de performances) à laquelle ce compteur de performances est associé.
* counterName : nom du compteur de performances.
* instanceName : nom de l'instance de catégorie de compteur de performances ou une chaîne vide ("") si la catégorie contient une seule instance. Si categoryName est Process et que le compteur de performance que vous souhaitez collecter vient du processus en cours de la JVM sur laquelle votre application s'exécute, spécifiez `"__SELF__"`.

### <a name="unix-performance-counters"></a>Compteurs de performances Unix
* [Installez collectd avec le plug-in Application Insights](java-collectd.md) pour obtenir une grande variété de données sur le système et le réseau.

## <a name="get-user-and-session-data"></a>Obtenir des données utilisateur et de session
Vous envoyez des données de télémétrie depuis votre serveur web. Vous pouvez désormais ajouter plus de surveillance pour obtenir une vue à 360 degrés de votre application :

* [Ajoutez la télémétrie à vos pages web][usage] pour surveiller les affichages de pages et les mesures relatives à l’utilisateur.
* [Configurez les tests web][availability] pour vous assurer que votre application est bien active.

## <a name="send-your-own-telemetry"></a>Envoyer votre propre télémétrie
Maintenant que vous avez installé le Kit de développement logiciel (SDK), vous pouvez utiliser l'API pour envoyer votre propre télémétrie.

* [Suivez des événements et des métriques personnalisés][api] pour savoir ce que les utilisateurs font avec votre application.
* [Recherchez les événements et les journaux d’activité][diagnostic] pour diagnostiquer les problèmes.

## <a name="availability-web-tests"></a>Tests web de disponibilité
Application Insights peut tester votre site web à intervalles réguliers pour vérifier qu’il fonctionne et répond correctement.

[En savoir plus sur la configuration des tests web de disponibilité.][availability]

## <a name="questions-problems"></a>Des questions ? Des problèmes ?
[Résolution des problèmes Java](java-troubleshoot.md)

## <a name="next-steps"></a>Étapes suivantes
* [Surveillance des appels de dépendance](java-agent.md)
* [Surveillance des compteurs de performances Unix](java-collectd.md)
* Ajoutez [la surveillance à vos pages web](javascript.md) pour surveiller le temps de chargement des pages, les appels AJAX et les exceptions du navigateur.
* Écrivez [télémétrie personnalisée](../../azure-monitor/app/api-custom-events-metrics.md) pour suivre l’utilisation sur le navigateur ou le serveur.
* Utilisez [Analytics](../../azure-monitor/app/analytics.md) pour des requêtes puissantes sur les données de télémétrie de votre application
* Pour plus d’informations, consultez [Azure pour les développeurs Java](/java/azure).

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[apiexceptions]: ../../azure-monitor/app/api-custom-events-metrics.md#trackexception
[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[javalogs]: java-trace-logs.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md
[usage]: javascript.md
