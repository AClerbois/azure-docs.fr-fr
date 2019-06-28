---
title: Erreurs de données des journaux de diagnostic Azure Stream Analytics
description: Cet article explique les différentes erreurs de données d’entrée et de sortie qui peuvent se produire lors de l’utilisation d’Azure Stream Analytics.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/09/2019
ms.openlocfilehash: b00eb12092838746f4bfe16f00eac55df9224b09
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65607227"
---
# <a name="azure-stream-analytics-data-errors"></a>Erreurs de données Azure Stream Analytics

Lorsqu’il existe une incohérence dans les données traitées par un travail Azure Stream Analytics, ce programme envoie un événement d’erreur de données aux journaux de diagnostic. Stream Analytics écrit des informations détaillées et des exemples d’événements dans ses journaux de diagnostic lorsque des erreurs de données se produisent. Un résumé de ces informations est également fourni via les notifications du portail pour certaines erreurs.

Cet article décrit les différents types d’erreurs, leurs causes et les détails des journaux de diagnostic pour les erreurs de données d’entrée et de sortie.

## <a name="diagnostic-log-schema"></a>Schéma des journaux de diagnostic

Pour connaître le schéma des journaux de diagnostic, consultez la section [Résoudre les problèmes liés à Azure Stream Analytics à l’aide des journaux de diagnostic](stream-analytics-job-diagnostic-logs.md#diagnostics-logs-schema). Le code JSON suivant est un exemple de valeur pour le champ **Propriétés** d’un journal de diagnostic en cas d’erreur de données.

```json
{
    "Source": "InputTelemetryData",
    "Type": "DataError",
    "DataErrorType": "InputDeserializerError.InvalidData",
    "BriefMessage": "Json input stream should either be an array of objects or line separated objects. Found token type: Integer",
    "Message": "Input Message Id: https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt Error: Json input stream should either be an array of objects or line separated objects. Found token type: Integer",
    "ExampleEvents": "[\"1,2\\\\u000d\\\\u000a3,4\\\\u000d\\\\u000a5,6\"]",
    "FromTimestamp": "2019-03-22T22:34:18.5664937Z",
    "ToTimestamp": "2019-03-22T22:34:18.5965248Z",
    "EventCount": 1
}
```

## <a name="input-data-errors"></a>Erreurs de données d’entrée

### <a name="inputdeserializererrorinvalidcompressiontype"></a>InputDeserializerError.InvalidCompressionType

* Cause : Le type de compression d’entrée sélectionné ne correspond pas aux données.
* Notification de portail fournie : OUI
* Niveau du journal de diagnostic : Avertissement
* Détails des journaux
   * Identificateur de message d’entrée. Pour Event Hub, l’identificateur est l’ID de partition, le décalage et le numéro de séquence.

**Message d’erreur**

```json
"BriefMessage": "Unable to decompress events from resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt'. Please ensure compression setting fits the data being processed."
```

### <a name="inputdeserializererrorinvalidheader"></a>InputDeserializerError.InvalidHeader

* Cause : L’en-tête de données d’entrée n’est pas valide. Par exemple, un fichier CSV comporte des colonnes avec des noms en double.
* Notification de portail fournie : OUI
* Niveau du journal de diagnostic : Avertissement
* Détails des journaux
   * Identificateur de message d’entrée. 
   * Charge utile réelle jusqu’à quelques kilo-octets.

**Message d’erreur**

```json
"BriefMessage": "Invalid CSV Header for resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt'. Please make sure there are no duplicate field names."
```

### <a name="inputdeserializererrormissingcolumns"></a>InputDeserializerError.MissingColumns

* Cause : Les colonnes d’entrée définies avec CREATE TABLE ou par le biais TIMESTAMP BY n’existent pas.
* Notification de portail fournie : OUI
* Niveau du journal de diagnostic : Avertissement
* Détails des journaux
   * Identificateur de message d’entrée. 
   * Noms des colonnes manquantes. 
   * Charge utile réelle jusqu’à quelques kilo-octets.

**Messages d’erreur**

```json
"BriefMessage": "Could not deserialize the input event(s) from resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt' as Csv. Some possible reasons: 1) Malformed events 2) Input source configured with incorrect serialization format" 
```

```json
"Message": "Missing fields specified in query or in create table. Fields expected:ColumnA Fields found:ColumnB"
```

### <a name="inputdeserializererrortypeconversionerror"></a>InputDeserializerError.TypeConversionError

* Cause : Impossible de convertir l’entrée selon le type spécifié dans l’instruction CREATE TABLE.
* Notification de portail fournie : OUI
* Niveau du journal de diagnostic : Avertissement
* Détails des journaux
   * Identificateur de message d’entrée. 
   * Nom de la colonne et type attendu.

**Messages d’erreur**

```json
"BriefMessage": "Could not deserialize the input event(s) from resource '''https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt ' as Csv. Some possible reasons: 1) Malformed events 2) Input source configured with incorrect serialization format" 
```

```json
"Message": "Unable to convert column: dateColumn to expected type."
```

### <a name="inputdeserializererrorinvaliddata"></a>InputDeserializerError.InvalidData

* Cause : Les données d’entrée ne sont pas au bon format. Par exemple, l’entrée n’est pas un JSON valide.
* Notification de portail fournie : OUI
* Niveau du journal de diagnostic : Avertissement
* Détails des journaux
   * Identificateur de message d’entrée. 
   * Charge utile réelle jusqu’à quelques kilo-octets.

**Messages d’erreur**

```json
"BriefMessage": "Json input stream should either be an array of objects or line separated objects. Found token type: String"
```

```json
"Message": "Json input stream should either be an array of objects or line separated objects. Found token type: String"
```

### <a name="invalidinputtimestamp"></a>InvalidInputTimeStamp

* Cause : La valeur de l’expression TIMESTAMP BY ne peut pas être convertie en DateHeure.
* Notification de portail fournie : OUI
* Niveau du journal de diagnostic : Avertissement
* Détails des journaux
   * Identificateur de message d’entrée. 
   * Message d’erreur. 
   * Charge utile réelle jusqu’à quelques kilo-octets.

**Message d’erreur**

```json
"BriefMessage": "Unable to get timestamp for resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt ' due to error 'Cannot convert string to datetime'"
```

### <a name="invalidinputtimestampkey"></a>InvalidInputTimeStampKey

* Cause : La valeur de TIMESTAMP BY OVER timestampColumn est NULL.
* Notification de portail fournie : OUI
* Niveau du journal de diagnostic : Avertissement
* Détails des journaux
   * Charge utile réelle jusqu’à quelques kilo-octets.

**Message d’erreur**

```json
"BriefMessage": "Unable to get value of TIMESTAMP BY OVER COLUMN"
```

### <a name="lateinputevent"></a>LateInputEvent

* Cause : La différence entre l’heure de l’application et l’heure d’arrivée est supérieure à la fenêtre de tolérance d’arrivée tardive.
* Notification de portail fournie : Non
* Niveau du journal de diagnostic : Information
* Détails des journaux
   * Heure de l’application et heure d’arrivée. 
   * Charge utile réelle jusqu’à quelques kilo-octets.

**Message d’erreur**

```json
"BriefMessage": "Input event with application timestamp '2019-01-01' and arrival time '2019-01-02' was sent later than configured tolerance."
```

### <a name="earlyinputevent"></a>EarlyInputEvent

* Cause : La différence entre l’heure de l’application et l’heure d’arrivée est supérieure à 5 minutes.
* Notification de portail fournie : Non
* Niveau du journal de diagnostic : Information
* Détails des journaux
   * Heure de l’application et heure d’arrivée. 
   * Charge utile réelle jusqu’à quelques kilo-octets.

**Message d’erreur**

```json
"BriefMessage": "Input event arrival time '2019-01-01' is earlier than input event application timestamp '2019-01-02' by more than 5 minutes."
```

### <a name="outoforderevent"></a>OutOfOrderEvent

* Cause : L’événement est considéré comme en désordre en raison de la fenêtre de tolérance de désordre définie.
* Notification de portail fournie : Non
* Niveau du journal de diagnostic : Information
* Détails des journaux
   * Charge utile réelle jusqu’à quelques kilo-octets.

**Message d’erreur**

```json
"Message": "Out of order event(s) received."
```

## <a name="output-data-errors"></a>Erreurs de données de sortie

### <a name="outputdataconversionerrorrequiredcolumnmissing"></a>OutputDataConversionError.RequiredColumnMissing

* Cause : La colonne obligatoire pour la sortie n’existe pas. Par exemple, une colonne définie comme clé de partition de table Azure n’existe pas.
* Notification de portail fournie : OUI
* Niveau du journal de diagnostic : Avertissement
* Détails des journaux
   * Nom de la colonne, et identificateur d’enregistrement ou partie de l’enregistrement.

**Message d’erreur**

```json
"Message": "The output record does not contain primary key property: [deviceId] Ensure the query output contains the column [deviceId] with a unique non-empty string less than '255' characters."
```

### <a name="outputdataconversionerrorcolumnnameinvalid"></a>OutputDataConversionError.ColumnNameInvalid

* Cause : La valeur de colonne n’est pas conforme à la sortie. Par exemple, le nom de colonne n’est pas une colonne de table Azure valide.
* Notification de portail fournie : OUI
* Niveau du journal de diagnostic : Avertissement
* Détails des journaux
   * Nom de la colonne, et identificateur d’enregistrement ou partie de l’enregistrement.

**Message d’erreur**

```json
"Message": "Invalid property name #deviceIdValue. Please refer MSDN for Azure table property naming convention."
```

### <a name="outputdataconversionerrortypeconversionerror"></a>OutputDataConversionError.TypeConversionError

* Cause : Une colonne ne peut pas être convertie en un type valide dans la sortie. Par exemple, la valeur de colonne est incompatible avec les contraintes ou le type définis dans la table SQL.
* Notification de portail fournie : OUI
* Niveau du journal de diagnostic : Avertissement
* Détails des journaux
   * Nom de la colonne.
   * Identificateur d’enregistrement ou partie de l’enregistrement.

**Message d’erreur**

```json
"Message": "The column [id] value null or its type is invalid. Ensure to provide a unique non-empty string less than '255' characters."
```

### <a name="outputdataconversionerrorrecordexceededsizelimit"></a>OutputDataConversionError.RecordExceededSizeLimit

* Cause : La valeur du message est supérieure à la taille de sortie prise en charge. Par exemple, un enregistrement fait plus de 1 Mo pour une sortie Event Hub.
* Notification de portail fournie : OUI
* Niveau du journal de diagnostic : Avertissement
* Détails des journaux
   * Identificateur d’enregistrement ou partie de l’enregistrement.

**Message d’erreur**

```json
"BriefMessage": "Single output event exceeds the maximum message size limit allowed (262144 bytes) by Event Hub."
```

### <a name="outputdataconversionerrorduplicatekey"></a>OutputDataConversionError.DuplicateKey

* Cause : Un enregistrement contient déjà une colonne portant le même nom qu’une colonne système. Par exemple, la sortie CosmosDB avec une colonne nommée ID lorsque la colonne d’ID est une autre colonne.
* Notification de portail fournie : OUI
* Niveau du journal de diagnostic : Avertissement
* Détails des journaux
   * Nom de la colonne.
   * Identificateur d’enregistrement ou partie de l’enregistrement.

```json
"BriefMessage": "Column 'devicePartitionKey' is being mapped to multiple columns."
```

## <a name="next-steps"></a>Étapes suivantes

* [Résoudre les problèmes liés à Azure Stream Analytics à l’aide des journaux de diagnostic](stream-analytics-job-diagnostic-logs.md)

* [Présentation de la surveillance des tâches Stream Analytics et des requêtes](stream-analytics-monitoring.md)
