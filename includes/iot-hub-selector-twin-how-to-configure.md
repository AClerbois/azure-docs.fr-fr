> [!div class="op_single_selector"]
> * [Node.JS](../articles/iot-hub/iot-hub-node-node-twin-how-to-configure.md)
> * [C#](../articles/iot-hub/iot-hub-csharp-node-twin-how-to-configure.md)
> 
> 

## <a name="introduction"></a>Introduction
Dans l’article [Prise en main des représentations d’appareil IoT Hub][lnk-twin-tutorial], vous avez appris à définir les métadonnées d’appareil à partir de votre serveur principal de solution à l’aide *d’étiquettes*, à signaler les conditions d’appareil à partir d’une application d’appareil au moyen de *propriétés signalées* et à interroger ces informations par le biais d’un langage de type SQL.

Dans ce didacticiel, vous allez découvrir comment combiner les *propriétés souhaitées* des représentations d’appareil et les *propriétés signalées* afin de configurer les applications d’appareil à distance. Plus précisément, ce didacticiel décrit la façon dont les propriétés signalées et souhaitées des représentations d’appareil permettent de procéder à une configuration à plusieurs étapes d’un paramètre d’application d’appareil, ainsi que la façon dont ces propriétés dotent le serveur principal de solution d’une visibilité de l’état de cette opération sur l’ensemble des appareils.

Globalement, ce didacticiel suit le *modèle d’état souhaité* pour la gestion des périphériques. L’idée fondamentale qui sous-tend ce modèle est que le serveur principal de solution spécifie l’état souhaité pour les appareils gérés, au lieu d’envoyer des commandes spécifiques. L’appareil doit donc établir lui-même la meilleure façon d’atteindre l’état souhaité (cet aspect revêt une grande importance dans les scénarios IoT dans lesquels des conditions d’appareil spécifiques ont une incidence sur la capacité d’exécution immédiate de commandes données), tout en signalant continuellement au serveur principal l’état actuel et les conditions d’erreur possibles. Le modèle d’état souhaité joue un rôle déterminant dans la gestion d’ensembles d’appareils volumineux, car il offre au serveur principal une visibilité totale de l’état du processus de configuration sur l’ensemble des appareils.
Pour plus d’informations sur le rôle du modèle d’état souhaité dans la gestion des appareils, consultez l’article [Vue d’ensemble de la gestion des appareils avec IoT Hub][lnk-dm-overview].

> [!NOTE]
> Pour les scénarios impliquant un contrôle plus interactif des appareils (mise en marche d’un ventilateur à partir d’une application contrôlée par l’utilisateur), envisagez d’utiliser des [méthodes directes][lnk-methods].
> 
> 

Dans ce didacticiel, le serveur principal d’applications modifie la configuration de télémétrie d’un appareil cible. En conséquence, l’application d’appareil suit un processus à plusieurs étapes pour appliquer une mise à jour de configuration (nécessitant par exemple un redémarrage de module logiciel), ce que ce didacticiel simule avec un simple délai.

Le serveur principal stocke la configuration dans les propriétés souhaitées des représentations d’appareil comme suit :

        {
            ...
            "properties": {
                ...
                "desired": {
                    "telemetryConfig": {
                        "configId": "{id of the configuration}",
                        "sendFrequency": "{config}"
                    }
                }
                ...
            }
            ...
        }

> [!NOTE]
> Étant donné que les configurations peuvent constituer des objets complexes, elles reçoivent généralement un ID qui leur est propre (codes de hachage ou [GUID][lnk-guid]) afin de simplifier leur comparaison.
> 
> 

L’application d’appareil spécifie sa configuration actuelle reflétant la propriété souhaitée **telemetryConfig** dans les propriétés signalées :

        {
            "properties": {
                ...
                "reported": {
                    "telemetryConfig": {
                        "changeId": "{id of the current configuration}",
                        "sendFrequency": "{current configuration}",
                        "status": "Success",
                    }
                }
                ...
            }
        }

Notez que la propriété **telemetryConfig** signalée présente une propriété **status** supplémentaire qui permet de spécifier l’état du processus de mise à jour de configuration.

Lorsqu’une nouvelle configuration souhaitée est reçue, l’application d’appareil signale une configuration en attente en modifiant les informations :

        {
            "properties": {
                ...
                "reported": {
                    "telemetryConfig": {
                        "changeId": "{id of the current configuration}",
                        "sendFrequency": "{current configuration}",
                        "status": "Pending",
                        "pendingConfig": {
                            "changeId": "{id of the pending configuration}",
                            "sendFrequency": "{pending configuration}"
                        }
                    }
                }
                ...
            }
        }

L’application d’appareil signalera alors par la suite la réussite ou l’échec de cette opération en mettant à jour la propriété ci-dessus.
Notez que le serveur principal peut à tout moment interroger l’état du processus de configuration sur l’ensemble des appareils.

Ce didacticiel vous explique les procédures suivantes :

* Création d’un appareil simulé qui reçoit des mises à jour de configuration à partir du serveur principal et signale les différentes mises à jour sous la forme de *propriétés signalées* dans le processus de mise à jour de configuration
* Création d’une application principale qui met à jour la configuration souhaitée d’un appareil, puis qui interroge le processus de mise à jour de configuration

<!-- links -->

[lnk-methods]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
[lnk-dm-overview]: ../articles/iot-hub/iot-hub-device-management-overview.md
[lnk-twin-tutorial]: ../articles/iot-hub/iot-hub-node-node-twin-getstarted.md
[lnk-guid]: https://en.wikipedia.org/wiki/Globally_unique_identifier

<!--HONumber=Nov16_HO3-->


