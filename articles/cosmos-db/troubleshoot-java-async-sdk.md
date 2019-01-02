---
title: Diagnostiquer et résoudre les problèmes du kit SDK Java Async Azure Cosmos DB
description: Utilisez des fonctionnalités telles que la journalisation côté client et d’autres outils tiers pour identifier, diagnostiquer et résoudre les problèmes liés à Azure Cosmos DB.
services: cosmos-db
author: moderakh
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 10/28/2018
ms.author: moderakh
ms.devlang: java
ms.component: cosmosdb-sql
ms.openlocfilehash: 951c26ea5b5c77cf205e7793834d564889b9a635
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52876101"
---
# <a name="troubleshoot-issues-when-you-use-the-java-async-sdk-with-azure-cosmos-db-sql-api-accounts"></a>Résoudre les problèmes quand vous utilisez le SDK Java Async avec des comptes d’API SQL Azure Cosmos DB
Cet article traite des problèmes courants, des solutions de contournement, des étapes de diagnostic et des outils quand vous utilisez le [SDK Java Async](sql-api-sdk-async-java.md) avec des comptes d’API SQL Azure Cosmos DB.
Le SDK Java Async fournit la représentation logique côté client pour accéder à l’API SQL Azure Cosmos DB. Cet article décrit les outils et les approches qui peuvent vous aider si vous rencontrez des problèmes.

Commencez par cette liste :

* Jetez un coup d’œil à la section [Problèmes courants et solutions de contournement] dans cet article.
* Consultez le SDK, qui est disponible [open source sur GitHub](https://github.com/Azure/azure-cosmosdb-java). Il contient une [section Problèmes](https://github.com/Azure/azure-cosmosdb-java/issues) qui est activement tenue à jour. Vérifiez si un problème similaire au vôtre dispose déjà d’une solution de contournement.
* Consultez les [conseils relatifs aux performances](performance-tips-async-java.md) et suivez les pratiques suggérées.
* Lisez le reste de cet article, si vous n’avez pas trouvé de solution. Ensuite, consignez un [problème GitHub](https://github.com/Azure/azure-cosmosdb-java/issues).

## <a name="common-issues-workarounds"></a>Problèmes courants et solutions de contournement

### <a name="network-issues-netty-read-timeout-failure-low-throughput-high-latency"></a>Problèmes réseau, échec du délai d’expiration de lecture Netty, débit faible, latence élevée

#### <a name="general-suggestions"></a>Recommandations d’ordre général
* Vérifiez que l’application s’exécute dans la même région que votre compte Azure Cosmos DB. 
* Vérifiez l’utilisation du processeur sur l’ordinateur hôte où l’application est en cours d’exécution. Si l’utilisation du processeur est supérieure ou égale à 90 %, exécutez votre application sur un hôte disposant d’une configuration plus élevée. Vous pouvez aussi distribuer la charge sur plusieurs ordinateurs.

#### <a name="connection-throttling"></a>Limitation de la connexion
La limitation de la connexion peut se produire en raison d’une [Limite de connexion sur un ordinateur hôte] ou d’une [insuffisance de ports Azure SNAT (PAT)].

##### <a name="connection-limit-on-host"></a>Limite de connexion sur un ordinateur hôte
Certains systèmes Linux, tels que Red Hat, ont une limite supérieure quant au nombre total de fichiers ouverts. Les sockets dans Linux étant implémentés en tant que fichiers, ce nombre limite aussi le nombre total de connexions.
Exécutez la commande ci-dessous.

```bash
ulimit -a
```
La quantité maximale de fichiers ouverts autorisée, qui sont identifiés comme « nofile », doit être au moins le double votre taille de pool de connexions. Pour plus d’informations, consultez [Conseils relatifs aux performances](performance-tips-async-java.md).

##### <a name="snat"></a>Insuffisance de ports Azure SNAT (PAT)

Si votre application est déployée sur Machine virtuelle Azure sans adresse IP publique, par défaut les [ports Azure SNAT](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports) établissent des connexions avec n’importe quel point de terminaison en dehors de votre machine virtuelle. Le nombre de connexions autorisées de la machine virtuelle au point de terminaison Azure Cosmos DB est limité par la [configuration Azure SNAT](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports).

 Les ports Azure SNAT sont utilisés uniquement quand votre machine virtuelle a une adresse IP privée et qu’un processus à partir de la machine virtuelle tente de se connecter avec une adresse IP publique. Il existe deux solutions de contournement pour éviter la limitation Azure SNAT :

* Ajoutez votre point de terminaison de service Azure Cosmos DB au sous-réseau de votre réseau virtuel Machines virtuelles Azure. Pour plus d’informations, consultez [Points de terminaison de service de réseau virtuel](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview). 

    Quand le point de terminaison de service est activé, les requêtes ne sont plus envoyées d’une adresse IP publique à Azure Cosmos DB. Au lieu de cela, les identités du réseau virtuel et du sous-réseau sont envoyées. Cette modification peut entraîner des problèmes de pare-feu si seules les adresses IP publiques sont autorisées. Si vous utilisez un pare-feu, quand vous activez le point de terminaison de service, ajoutez un sous-réseau au pare-feu à l’aide de [Listes de contrôle d’accès de réseau virtuel](https://docs.microsoft.com/azure/virtual-network/virtual-networks-acl).
* Assignez une adresse IP publique à votre machine virtuelle Azure.

#### <a name="http-proxy"></a>Serveur proxy HTTP

Si vous utilisez un proxy HTTP, vérifiez qu’il peut prendre en charge le nombre de connexions configuré dans SDK `ConnectionPolicy`.
Sinon, vous serez confronté à des problèmes de connexion.

#### <a name="invalid-coding-pattern-blocking-netty-io-thread"></a>Modèle de codage non valide : blocage du thread d’E/S Netty

Le SDK utilise la bibliothèque d’E/S [Netty](https://netty.io/) pour communiquer avec Azure Cosmos DB. Le SDK a des API Async et utilise des API d’E/S non bloquantes fournies par Netty. Les tâches d’E/S du SDK sont effectuées sur les threads d’E/S de Netty. Le nombre de threads d’E/S de Netty est configuré pour être identique au nombre de cœurs de processeur de l’ordinateur de l’application. 

Les threads d’E/S de Netty sont destinés à être utilisés uniquement pour les tâches d’E/S non bloquantes de Netty. Le SDK retourne le résultat d’appel de l’API sur l’un des threads d’E/S de Netty au code de l’application. Si l’application effectue une opération de longue durée après avoir reçu les résultats sur le thread Netty, le SDK risque de ne pas avoir suffisamment de threads d’E/S pour effectuer ses tâches d’E/S internes. Ce type de codage d’application peut entraîner un débit faible, une latence élevée et des échecs `io.netty.handler.timeout.ReadTimeoutException`. La solution de contournement consiste à basculer le thread quand vous savez que l’opération prendra de temps.

Par exemple, examinons l’extrait de code suivant. Il se peut que vous effectuiez un travail de longue durée qui prend plus de quelques millisecondes sur le thread Netty. Dans ce cas, vous risquez de passer dans un état où aucun thread d’E/S Netty n’est présent pour traiter les tâches d’E/S. Vous obtenez alors un échec ReadTimeoutException.
```java
@Test
public void badCodeWithReadTimeoutException() throws Exception {
    int requestTimeoutInSeconds = 10;

    ConnectionPolicy policy = new ConnectionPolicy();
    policy.setRequestTimeoutInMillis(requestTimeoutInSeconds * 1000);

    AsyncDocumentClient testClient = new AsyncDocumentClient.Builder()
            .withServiceEndpoint(TestConfigurations.HOST)
            .withMasterKeyOrResourceToken(TestConfigurations.MASTER_KEY)
            .withConnectionPolicy(policy)
            .build();

    int numberOfCpuCores = Runtime.getRuntime().availableProcessors();
    int numberOfConcurrentWork = numberOfCpuCores + 1;
    CountDownLatch latch = new CountDownLatch(numberOfConcurrentWork);
    AtomicInteger failureCount = new AtomicInteger();

    for (int i = 0; i < numberOfConcurrentWork; i++) {
        Document docDefinition = getDocumentDefinition();
        Observable<ResourceResponse<Document>> createObservable = testClient
                .createDocument(getCollectionLink(), docDefinition, null, false);
        createObservable.subscribe(r -> {
                    try {
                        // Time-consuming work is, for example,
                        // writing to a file, computationally heavy work, or just sleep.
                        // Basically, it's anything that takes more than a few milliseconds.
                        // Doing such operations on the IO Netty thread
                        // without a proper scheduler will cause problems.
                        // The subscriber will get a ReadTimeoutException failure.
                        TimeUnit.SECONDS.sleep(2 * requestTimeoutInSeconds);
                    } catch (Exception e) {
                    }
                },

                exception -> {
                    //It will be io.netty.handler.timeout.ReadTimeoutException.
                    exception.printStackTrace();
                    failureCount.incrementAndGet();
                    latch.countDown();
                },
                () -> {
                    latch.countDown();
                });
    }

    latch.await();
    assertThat(failureCount.get()).isGreaterThan(0);
}
```
   La solution de contournement consiste à changer le thread sur lequel vous effectuez le travail qui prend du temps. Définissez une instance singleton du planificateur pour votre application.
   ```java
// Have a singleton instance of an executor and a scheduler.
ExecutorService ex  = Executors.newFixedThreadPool(30);
Scheduler customScheduler = rx.schedulers.Schedulers.from(ex);
   ```
   Vous devrez peut-être effectuer des tâches qui prennent du temps, par exemple des E/S bloquantes ou des tâches qui nécessitent beaucoup de ressources de calcul. Dans ce cas, basculez vers un thread de travail fourni par votre `customScheduler` à l’aide de l’API `.observeOn(customScheduler)`.
```java
Observable<ResourceResponse<Document>> createObservable = client
        .createDocument(getCollectionLink(), docDefinition, null, false);

createObservable
        .observeOn(customScheduler) // Switches the thread.
        .subscribe(
            // ...
        );
```
En utilisant `observeOn(customScheduler)`, vous libérez le thread d’E/S Netty et basculez vers votre propre thread personnalisé fourni par le planificateur personnalisé. Cette modification résout le problème. Vous n’obtiendrez plus d’échec `io.netty.handler.timeout.ReadTimeoutException`.

### <a name="connection-pool-exhausted-issue"></a>Problème de pool de connexions épuisé

`PoolExhaustedException` est un échec côté client. Il indique que votre charge de travail d’application est plus élevée que ce que le pool de connexions SDK peut gérer. Augmentez la taille du pool de connexions ou répartissez la charge sur plusieurs applications.

### <a name="request-rate-too-large"></a>Taux de requêtes trop élevé
Cet échec est une erreur côté serveur. Il indique que vous avez consommé votre débit provisionné. Réessayez ultérieurement. Si vous obtenez souvent cet échec, augmentez le débit de la collection.

### <a name="failure-connecting-to-azure-cosmos-db-emulator"></a>Échec de connexion à l’émulateur Azure Cosmos DB

Le certificat HTTPS de l’émulateur Azure Cosmos DB est auto-signé. Pour que le SDK fonctionne avec l’émulateur, importez le certificat de l’émulateur dans Java TrustStore. Pour plus d’informations, consultez [Exporter les certificats de l’émulateur Azure Cosmos DB](local-emulator-export-ssl-certificates.md).


## <a name="enable-client-sice-logging"></a>Activer la journalisation de SDK client

Le SDK Java Async utilise SLF4j en tant que façade de journalisation qui prend en charge la journalisation dans les frameworks de journalisation populaires comme log4j et logback.

Par exemple, si vous souhaitez utiliser log4j en tant que framework de journalisation, ajoutez les bibliothèques suivantes dans votre chemin de classe Java.

```xml
<dependency>
  <groupId>org.slf4j</groupId>
  <artifactId>slf4j-log4j12</artifactId>
  <version>${slf4j.version}</version>
</dependency>
<dependency>
  <groupId>log4j</groupId>
  <artifactId>log4j</artifactId>
  <version>${log4j.version}</version>
</dependency>
```

Ajoutez également une configuration log4j.
```
# this is a sample log4j configuration

# Set root logger level to DEBUG and its only appender to A1.
log4j.rootLogger=INFO, A1

log4j.category.com.microsoft.azure.cosmosdb=DEBUG
#log4j.category.io.netty=INFO
#log4j.category.io.reactivex=INFO
# A1 is set to be a ConsoleAppender.
log4j.appender.A1=org.apache.log4j.ConsoleAppender

# A1 uses PatternLayout.
log4j.appender.A1.layout=org.apache.log4j.PatternLayout
log4j.appender.A1.layout.ConversionPattern=%d %5X{pid} [%t] %-5p %c - %m%n
```

Pour plus d’informations, consultez le [manuel de journalisation sfl4j](https://www.slf4j.org/manual.html).

## <a name="netstats"></a>Statistiques réseau du système d’exploitation
Exécutez la commande netstat pour avoir une idée du nombre de connexions à l’état `ESTABLISHED`, `CLOSE_WAIT`, et ainsi de suite.

Sur Linux, vous pouvez exécuter la commande suivante.
```bash
netstat -nap
```
Filtrez le résultat uniquement sur les connexions au point de terminaison Azure Cosmos DB.

Le nombre de connexions au point de terminaison Azure Cosmos DB à l’état `ESTABLISHED` ne doit pas être supérieur à la taille du pool de connexions configuré.

De nombreuses connexions au point de terminaison Azure Cosmos DB peuvent se trouver à l’état `CLOSE_WAIT`. Il peut y en avoir plus de 1000. Un nombre si élevé indique que les connexions sont établies et détruites rapidement. Cette situation peut provoquer des problèmes. Pour plus d’informations, consultez la section [Problèmes courants et solutions de contournement].

 <!--Anchors-->
[Problèmes courants et solutions de contournement]: #common-issues-workarounds
[Enable client SDK logging]: #enable-client-sice-logging
[Limite de connexion sur un ordinateur hôte]: #connection-limit-on-host
[Insuffisance de ports Azure SNAT (PAT)]: #snat


