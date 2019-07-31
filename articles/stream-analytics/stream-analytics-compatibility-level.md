---
title: Comprendre le niveau de compatibilité pour les travaux Azure Stream Analytics
description: Apprenez à définir un niveau de compatibilité pour un travail Azure Stream Analytics et découvrez les principales modifications apportées au dernier niveau de compatibilité
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 5/2/2019
ms.openlocfilehash: 8e3ae84242a1a9b76fbb18a8d8164c97a62a97d9
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/15/2019
ms.locfileid: "68003910"
---
# <a name="compatibility-level-for-azure-stream-analytics-jobs"></a>Niveau de compatibilité pour les travaux Azure Stream Analytics

Cet article décrit l’option de niveau de compatibilité d’Azure Stream Analytics. Stream Analytics est un service managé régulièrement enrichi de nouvelles fonctionnalités et d’améliorations des performances. La plupart des mises à jour de runtime du service sont automatiquement accessibles aux utilisateurs finals. 

Toutefois, certaines nouvelles fonctionnalités du service introduisent parfois un changement majeur, tel qu’une modification du comportement d’un travail existant, ou une évolution du mode de consommation des données dans les travaux en cours d’exécution. Si vous souhaitez que vos travaux Stream Analytics existants continuent de s’exécuter sans changement majeur, laissez le paramètre de niveau de compatibilité défini sur une valeur faible. Lorsque vous êtes prêt à intégrer les derniers comportements de runtime, vous pouvez les accepter en augmentant le niveau de compatibilité. 

## <a name="choose-a-compatibility-level"></a>Choisir un niveau de compatibilité

Le niveau de compatibilité contrôle le comportement d’exécution d’un travail Stream Analytics. 

Azure Stream Analytics prend actuellement en charge trois niveaux de compatibilité :

* 1.0 : comportement précédent
* 1.1 : comportement par défaut
* 1.2 (préversion) : nouveau comportement intégrant les dernières améliorations en phase d’évaluation

Le niveau de compatibilité 1.0 initial a été introduit dans la version en disponibilité générale d’Azure Stream Analytics voici quelques années.

Lorsque vous créez un travail Stream Analytics, il est recommandé de le créer à l’aide du dernier niveau de compatibilité. Démarrez votre conception de travail en vous basant sur les derniers comportements afin d’éviter un ajout de complexité et de modifications par la suite.

## <a name="set-the-compatibility-level"></a>Définition du niveau de compatibilité

Vous pouvez définir le niveau de compatibilité d’un travail Stream Analytics dans le Portail Azure ou en appelant [l’API REST de création d’un travail](/rest/api/streamanalytics/stream-analytics-job).

Pour mettre à jour le niveau de compatibilité du travail dans le Portail Azure :

1. Utilisez le [Portail Azure](https://portal.azure.com) pour accéder à votre travail Stream Analytics.
2. Interrompez le travail à l’aide de la commande **Arrêter** avant de mettre à jour le niveau de compatibilité. Si votre travail est en cours d’exécution, vous ne peut pas mettre à jour le niveau de compatibilité.
3. Sous le titre **Configurer**, sélectionnez **Niveau de compatibilité**.
4. Choisissez la valeur de niveau de compatibilité souhaitée.
5. Sélectionnez **Enregistrer** au bas de la page.

![Niveau de compatibilité Stream Analytics dans le portail Azure](media/stream-analytics-compatibility-level/stream-analytics-compatibility.png)

Lorsque vous mettez à jour le niveau de compatibilité, le compilateur T-SQL valide le travail avec la syntaxe correspondant au niveau de compatibilité sélectionné.

## <a name="compatibility-level-12-preview"></a>Niveau de compatibilité 1.2 (préversion)

Les principales modifications introduites dans le niveau de compatibilité 1.2 sont les suivantes :

### <a name="geospatial-functions"></a>Fonctions géospatiales

**Niveaux précédents :** Azure Stream Analytics utilisait des calculs de type Géographie.

**Niveau 1.2 :** Azure Stream Analytics vous permet de calculer des coordonnées géographiques projetées de type Géométrique. Il n’existe aucune modification au niveau de la signature des fonctions géospatiales. Toutefois, leur sémantique a légèrement changé et améliore la précision des calculs.

Azure Stream Analytics prend en charge l’indexation des données de référence géospatiales. Les données de référence contenant des éléments géospatiaux peuvent être indexés afin d’accélérer les calculs.

Les fonctions géospatiales mises à jour offrent l’expressivité totale du format géospatial WKT (Well Known Text). Vous pouvez spécifier d’autres composants géospatiaux qui n’étaient précédemment pas pris en charge avec GeoJson.

Pour plus d’informations, consultez le billet de blog [Updates to geospatial features in Azure Stream Analytics – Cloud and IoT Edge](https://azure.microsoft.com/blog/updates-to-geospatial-functions-in-azure-stream-analytics-cloud-and-iot-edge/) (Mises à jour des fonctionnalités géospatiales dans Azure Stream Analytics - Cloud et IoT Edge).

### <a name="parallel-query-execution-for-input-sources-with-multiple-partitions"></a>Exécution de requêtes en parallèle pour les sources d’entrée avec plusieurs partitions

**Niveaux précédents :** les requêtes Azure Stream Analytics nécessitaient l’utilisation de la clause PARTITION BY pour paralléliser le traitement des requêtes dans l’ensemble des partitions de source d’entrée.

**Niveau 1.2 :** si la logique de requête peut être parallélisée sur toutes les partitions de source d’entrée, Azure Stream Analytics crée des instances de requête distinctes et exécute les calculs en parallèle.

### <a name="native-bulk-api-integration-with-cosmosdb-output"></a>Intégration de l’API en bloc en mode natif à la sortie CosmosDB

**Niveaux précédents :** le comportement de l’opération upsert était *insert or merge* (insérer ou fusionner).

**Niveau 1.2 :** l’intégration de l’API en bloc en mode natif à la sortie CosmosDB optimise le débit et gère efficacement les requêtes de limitation. Pour plus d’informations, consultez l’article [Azure Stream Analytics output to Azure Cosmos DB](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-documentdb-output#improved-throughput-with-compatibility-level-12) (Sortie Azure Stream Analytics dans Azure Cosmos DB).

Le comportement de l’opération upsert est *insert or replace* (insérer ou remplacer).

### <a name="datetimeoffset-when-writing-to-sql-output"></a>Utilisation de DateTimeOffset lors de l’écriture dans une sortie SQL

**Niveaux précédents :** les types [DateTimeOffset](https://docs.microsoft.com/sql/t-sql/data-types/datetimeoffset-transact-sql?view=sql-server-2017) étaient ajustés au format temps universel coordonné (UTC).

**Niveau 1.2 :** DateTimeOffset n’est plus ajusté.

### <a name="long-when-writing-to-sql-output"></a>Long lors de l’écriture dans une sortie SQL

**Niveaux précédents :** Les valeurs ont été tronquées en fonction du type cible.

**Niveau 1.2 :** Les valeurs qui ne tiennent pas dans le type cible sont traitées en fonction de la stratégie d’erreur de sortie.

### <a name="strict-validation-of-prefix-of-functions"></a>Validation stricte des préfixes de fonction

**Niveaux précédents :** il n’existait aucune validation stricte des préfixes de fonction.

**Niveau 1.2 :** Azure Stream Analytics applique une validation stricte des préfixes de fonction. L’ajout d’un préfixe à une fonction intégrée entraîne une erreur. Par exemple, `myprefix.ABS(…)` n’est pas pris en charge.

L’ajout d’un préfixe à des agrégats intégrés engendre également une erreur. Par exemple, `myprefix.SUM(…)` n’est pas pris en charge.

L’utilisation du préfixe "system" pour des fonctions définies par l’utilisateur entraîne une erreur.

### <a name="disallow-array-and-object-as-key-properties-in-cosmos-db-output-adapter"></a>Interdiction des types Array et Object en tant que propriétés de clé dans l’adaptateur de sortie Cosmos DB

**Niveaux précédents :** les types Array et Object étaient pris en charge comme propriétés de clé.

**Niveau 1.2 :** les types Array et Object ne sont plus pris en charge comme propriétés de clé.

## <a name="compatibility-level-11"></a>Niveau de compatibilité 1.1

Voici les principales modifications introduites dans le niveau de compatibilité 1.1 :

### <a name="service-bus-xml-format"></a>Format XML de Service Bus

**Niveau 1.0 :** Azure Stream Analytics utilisait DataContractSerializer, c’est pourquoi le contenu des messages incluait des balises XML. Par exemple :

`@\u0006string\b3http://schemas.microsoft.com/2003/10/Serialization/\u0001{ "SensorId":"1", "Temperature":64\}\u0001`

**Niveau 1.1 :** le contenu du message renferme directement le flux sans aucune balise supplémentaire. Par exemple : `{ "SensorId":"1", "Temperature":64}`

### <a name="persisting-case-sensitivity-for-field-names"></a>Conservation de la casse des noms de champ

**Niveau 1.0 :** les noms de champs passaient en minuscules au moment du traitement par le moteur Azure Stream Analytics.

**Niveau 1.1 :** la casse des noms de champ est préservée lorsque ces derniers sont traités par le moteur Azure Stream Analytics.

> [!NOTE]
> La conservation de la casse n’est pas encore disponible pour les tâches Stream Analytics hébergés à l’aide de l’environnement Edge. Par conséquent, tous les noms de champs sont convertis en minuscules si votre tâche est hébergée sur Edge.

### <a name="floatnandeserializationdisabled"></a>FloatNaNDeserializationDisabled

**Niveau 1.0 :** la commande CREATE TABLE ne filtrait pas les événements avec une valeur NaN (Not-a-Number. Par exemple, Infinity, -Infinity) dans une colonne de type FLOAT, car ils étaient en dehors des limites documentées pour ces numéros.

**Niveau 1.1 :** la commande CREATE TABLE vous permet de spécifier un schéma fort. Le moteur Stream Analytics vérifie que les données sont conformes à ce schéma. Avec ce modèle, la commande peut filtrer des événements avec des valeurs NaN.

### <a name="disable-automatic-upcast-for-datetime-strings-in-json"></a>Désactivation de l’hyperonyme automatique pour les chaînes DateHeure au format JSON

**Niveau 1.0 :** l’analyseur JSON upcastait automatiquement en type DateTime les valeurs de chaîne contenant des informations de type date/heure/fuseau horaire et les convertissait au format UTC. Ce comportement entraînait la perte des informations relatives au fuseau horaire.

**Niveau 1.1 :** il n’existe plus aucun upcast automatique des valeurs de chaîne avec des informations de date/heure/fuseau horaire en type DateTime. Les informations relatives au fuseau horaire sont donc conservées.

## <a name="next-steps"></a>Étapes suivantes

* [Résoudre les problèmes liés aux entrées Azure Stream Analytics](stream-analytics-troubleshoot-input.md)
* [Stream Analytics Resource health](stream-analytics-resource-health.md) (Intégrité des ressources Stream Analytics)
