---
title: Schémas de suivi X12 pour les messages B2B - Azure Logic Apps | Microsoft Docs
description: Créer des schémas de suivi X12 qui surveillent les messages B2B dans les comptes d’intégration pour Azure Logic Apps avec Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: a5413f80-eaad-4bcf-b371-2ad0ef629c3d
ms.date: 01/27/2017
ms.openlocfilehash: 1db324006e1e6332b5fdd8afd28ebed8a32ac707
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60845764"
---
# <a name="create-schemas-for-tracking-x12-messages-in-integration-accounts-for-azure-logic-apps"></a>Créer des schémas pour le suivi des messages X12 et des MDN dans les comptes d’intégration pour Azure Logic Apps

Pour surveiller le succès, les erreurs et les propriétés des messages lors de transactions interentreprises (B2B), vous pouvez utiliser ces schémas de suivi X12 dans votre compte d’intégration :

* Schéma de suivi de document informatisé X12
* Schéma de suivi d’accusé de réception de document informatisé X12
* Schéma de suivi d’échange X12
* Schéma de suivi d’accusé de réception d’échange X12
* Schéma de suivi de groupe fonctionnel X12
* Schéma de suivi d’accusé de réception de groupe fonctionnel X12

## <a name="x12-transaction-set-tracking-schema"></a>Schéma de suivi de document informatisé X12

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "functionalGroupControlNumber": "",
      "transactionSetControlNumber": "",
      "CorrelationMessageId": "",
      "messageType": "",
      "isMessageFailed": "",
      "isTechnicalAcknowledgmentExpected": "",
      "isFunctionalAcknowledgmentExpected": "",
      "needAk2LoopForValidMessages":  "",
      "segmentsCount": ""
   }
}
```

| Propriété | Type | Description |
| --- | --- | --- |
| senderPartnerName | String | Nom de partenaire de l’expéditeur du message X12. (facultatif) |
| receiverPartnerName | String | Nom de partenaire du destinataire du message X12. (facultatif) |
| senderQualifier | String | Qualificateur du partenaire d’envoi. (obligatoire) |
| senderIdentifier | String | Identificateur du partenaire d’envoi. (obligatoire) |
| receiverQualifier | String | Qualificateur du partenaire de réception. (obligatoire) |
| receiverIdentifier | String | Identificateur du partenaire de réception. (obligatoire) |
| agreementName | String | Nom du contrat X12 dans lequel les messages sont résolus. (facultatif) |
| direction | Enum | Direction du flux de messages (envoi ou réception). (obligatoire) |
| interchangeControlNumber | String | Numéro de contrôle de l’échange. (facultatif) |
| functionalGroupControlNumber | String | Numéro de contrôle fonctionnel. (facultatif) |
| transactionSetControlNumber | String | Numéro de contrôle de document informatisé. (facultatif) |
| CorrelationMessageId | String | ID de message de corrélation. Correspond à {AgreementName} *{GroupControlNumber}* {TransactionSetControlNumber}. (facultatif) |
| messageType | String | Type de document ou de document informatisé. (facultatif) |
| isMessageFailed | Boolean | Indique si le message X12 a échoué. (obligatoire) |
| isTechnicalAcknowledgmentExpected | Boolean | Indique si l’accusé de réception technique est configuré dans le contrat X12. (obligatoire) |
| isFunctionalAcknowledgmentExpected | Boolean | Indique si l’accusé de réception fonctionnel est configuré dans le contrat X12. (obligatoire) |
| needAk2LoopForValidMessages | Boolean | Indique si la boucle AK2 est nécessaire pour un message valide. (obligatoire) |
| segmentsCount | Entier | Nombre de segments dans le document informatisé X12. (facultatif) |
||||

## <a name="x12-transaction-set-acknowledgement-tracking-schema"></a>Schéma de suivi d’accusé de réception de document informatisé X12

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "functionalGroupControlNumber": "",
      "isaSegment": "",
      "gsSegment": "",
      "respondingfunctionalGroupControlNumber": "",
      "respondingFunctionalGroupId": "",
      "respondingtransactionSetControlNumber": "",
      "respondingTransactionSetId": "",
      "statusCode": "",
      "processingStatus": "",
      "CorrelationMessageId": "",
      "isMessageFailed": "",
      "ak2Segment": "",
      "ak3Segment": "",
      "ak5Segment": ""
   }
}
```

| Propriété | Type | Description |
| --- | --- | --- |
| senderPartnerName | Chaîne | Nom de partenaire de l’expéditeur du message X12. (facultatif) |
| receiverPartnerName | Chaîne | Nom de partenaire du destinataire du message X12. (facultatif) |
| senderQualifier | Chaîne | Qualificateur du partenaire d’envoi. (obligatoire) |
| senderIdentifier | Chaîne | Identificateur du partenaire d’envoi. (obligatoire) |
| receiverQualifier | Chaîne | Qualificateur du partenaire de réception. (obligatoire) |
| receiverIdentifier | Chaîne | Identificateur du partenaire de réception. (obligatoire) |
| agreementName | Chaîne | Nom du contrat X12 dans lequel les messages sont résolus. (facultatif) |
| direction | Enum | Direction du flux de messages (envoi ou réception). (obligatoire) |
| interchangeControlNumber | Chaîne | Numéro de contrôle de l’échange de l’accusé de réception fonctionnel. Valeur renseignée uniquement pour l’envoi si un accusé de réception fonctionnel a été reçu pour les messages envoyés au partenaire. (facultatif) |
| functionalGroupControlNumber | Chaîne | Numéro de contrôle de groupe fonctionnel de l’accusé de réception fonctionnel. Valeur renseignée uniquement pour l’envoi si un accusé de réception fonctionnel a été reçu pour les messages envoyés au partenaire. (facultatif) |
| isaSegment | Chaîne | Segment ISA du message. Valeur renseignée uniquement pour l’envoi si un accusé de réception fonctionnel a été reçu pour les messages envoyés au partenaire. (facultatif) |
| gsSegment | Chaîne | Segment GS du message. Valeur renseignée uniquement pour l’envoi si un accusé de réception fonctionnel a été reçu pour les messages envoyés au partenaire. (facultatif) |
| respondingfunctionalGroupControlNumber | Chaîne | Numéro de contrôle de l’échange de réponse. (facultatif) |
| respondingFunctionalGroupId | Chaîne | ID du groupe fonctionnel de réponse qui est mappé à AK101 dans l’accusé de réception. (facultatif) |
| respondingtransactionSetControlNumber | Chaîne | Numéro de contrôle de document informatisé de réponse. (facultatif) |
| respondingTransactionSetId | Chaîne | ID du document informatisé de réponse, qui est mappé à AK201 dans l’accusé de réception. (facultatif) |
| statusCode | Boolean | Code d’état de l’accusé de réception du document informatisé. (obligatoire) |
| segmentsCount | Enum | Code d’état de l’accusé de réception. Les valeurs autorisées sont **Accepted**, **Rejected** et **AcceptedWithErrors**. (obligatoire) |
| processingStatus | Enum | État de traitement de l’accusé de réception. Les valeurs autorisées sont **Received**, **Generated** ou **Sent**. (obligatoire) |
| CorrelationMessageId | Chaîne | ID de message de corrélation. Correspond à {AgreementName} *{GroupControlNumber}* {TransactionSetControlNumber}. (facultatif) |
| isMessageFailed | Boolean | Indique si le message X12 a échoué. (obligatoire) |
| ak2Segment | Chaîne | Accusé de réception pour un document informatisé dans le groupe fonctionnel reçu. (facultatif) |
| ak3Segment | Chaîne | Signale les erreurs dans un segment de données. (facultatif) |
| ak5Segment | Chaîne | Signale si le document informatisé identifié dans le segment AK2 est accepté ou rejeté et pourquoi. (facultatif) |
||||

## <a name="x12-interchange-tracking-schema"></a>Schéma de suivi d’échange X12

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "isaSegment": "",
      "isTechnicalAcknowledgmentExpected": "",
      "isMessageFailed": "",
      "isa09": "",
      "isa10": "",
      "isa11": "",
      "isa12": "",
      "isa14": "",
      "isa15": "",
      "isa16": ""
   }
}
```

| Propriété | Type | Description |
| --- | --- | --- |
| senderPartnerName | Chaîne | Nom de partenaire de l’expéditeur du message X12. (facultatif) |
| receiverPartnerName | Chaîne | Nom de partenaire du destinataire du message X12. (facultatif) |
| senderQualifier | Chaîne | Qualificateur du partenaire d’envoi. (obligatoire) |
| senderIdentifier | Chaîne | Identificateur du partenaire d’envoi. (obligatoire) |
| receiverQualifier | Chaîne | Qualificateur du partenaire de réception. (obligatoire) |
| receiverIdentifier | Chaîne | Identificateur du partenaire de réception. (obligatoire) |
| agreementName | Chaîne | Nom du contrat X12 dans lequel les messages sont résolus. (facultatif) |
| direction | Enum | Direction du flux de messages (envoi ou réception). (obligatoire) |
| interchangeControlNumber | Chaîne | Numéro de contrôle de l’échange. (facultatif) |
| isaSegment | Chaîne | Segment ISA du message. (facultatif) |
| isTechnicalAcknowledgmentExpected | Boolean | Indique si l’accusé de réception technique est configuré dans le contrat X12. (obligatoire) |
| isMessageFailed | Boolean | Indique si le message X12 a échoué. (obligatoire) |
| isa09 | Chaîne | Date d’échange du document X12. (facultatif) |
| isa10 | Chaîne | Heure d’échange du document X12. (facultatif) |
| isa11 | Chaîne | Identificateur des normes de contrôle d’échange X12. (facultatif) |
| isa12 | Chaîne | Numéro de version du contrôle d’échange X12. (facultatif) |
| isa14 | Chaîne | Un accusé de réception X12 est exigé. (facultatif) |
| isa15 | Chaîne | Indicateur de test ou de production. (facultatif) |
| isa16 | Chaîne | Séparateur d'éléments. (facultatif) |
||||

## <a name="x12-interchange-acknowledgement-tracking-schema"></a>Schéma de suivi d’accusé de réception d’échange X12

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "isaSegment": "",
      "respondingInterchangeControlNumber": "",
      "isMessageFailed": "",
      "statusCode": "",
      "processingStatus": "",
      "ta102": "",
      "ta103": "",
      "ta105": ""
   }
}
```

| Propriété | Type | Description |
| --- | --- | --- |
| senderPartnerName | Chaîne | Nom de partenaire de l’expéditeur du message X12. (facultatif) |
| receiverPartnerName | Chaîne | Nom de partenaire du destinataire du message X12. (facultatif) |
| senderQualifier | Chaîne | Qualificateur du partenaire d’envoi. (obligatoire) |
| senderIdentifier | Chaîne | Identificateur du partenaire d’envoi. (obligatoire) |
| receiverQualifier | Chaîne | Qualificateur du partenaire de réception. (obligatoire) |
| receiverIdentifier | Chaîne | Identificateur du partenaire de réception. (obligatoire) |
| agreementName | Chaîne | Nom du contrat X12 dans lequel les messages sont résolus. (facultatif) |
| direction | Enum | Direction du flux de messages (envoi ou réception). (obligatoire) |
| interchangeControlNumber | Chaîne | Numéro de contrôle de l’échange de l’accusé de réception technique reçu de partenaires. (facultatif) |
| isaSegment | Chaîne | Segment ISA de l’accusé de réception technique reçu de partenaires. (facultatif) |
| respondingInterchangeControlNumber |Chaîne | Numéro de contrôle de l’échange pour l’accusé de réception technique reçu de partenaires. (facultatif) |
| isMessageFailed | Boolean | Indique si le message X12 a échoué. (obligatoire) |
| statusCode | Enum | Code d’état de l’accusé de réception de l’échange. Les valeurs autorisées sont **Accepted**, **Rejected** et **AcceptedWithErrors**. (obligatoire) |
| processingStatus | Enum | État de l’accusé de réception. Les valeurs autorisées sont **Received**, **Generated** ou **Sent**. (obligatoire) |
| ta102 | Chaîne | Date de l’échange. (facultatif) |
| ta103 | Chaîne | Heure de l’échange. (facultatif) |
| ta105 | Chaîne | Code de note de l’échange. (facultatif) |
||||

## <a name="x12-functional-group-tracking-schema"></a>Schéma de suivi de groupe fonctionnel X12

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "functionalGroupControlNumber": "",
      "gsSegment": "",
      "isTechnicalAcknowledgmentExpected": "",
      "isFunctionalAcknowledgmentExpected": "",
      "isMessageFailed": "",
      "gs01": "",
      "gs02": "",
      "gs03": "",
      "gs04": "",
      "gs05": "",
      "gs07": "",
      "gs08": ""
   }
}
```

| Propriété | Type | Description |
| --- | --- | --- |
| senderPartnerName | Chaîne | Nom de partenaire de l’expéditeur du message X12. (facultatif) |
| receiverPartnerName | Chaîne | Nom de partenaire du destinataire du message X12. (facultatif) |
| senderQualifier | Chaîne | Qualificateur du partenaire d’envoi. (obligatoire) |
| senderIdentifier | Chaîne | Identificateur du partenaire d’envoi. (obligatoire) |
| receiverQualifier | Chaîne | Qualificateur du partenaire de réception. (obligatoire) |
| receiverIdentifier | Chaîne | Identificateur du partenaire de réception. (obligatoire) |
| agreementName | Chaîne | Nom du contrat X12 dans lequel les messages sont résolus. (facultatif) |
| direction | Enum | Direction du flux de messages (envoi ou réception). (obligatoire) |
| interchangeControlNumber | Chaîne | Numéro de contrôle de l’échange. (facultatif) |
| functionalGroupControlNumber | Chaîne | Numéro de contrôle fonctionnel. (facultatif) |
| gsSegment | Chaîne | Segment GS de message. (facultatif) |
| isTechnicalAcknowledgmentExpected | Boolean | Indique si l’accusé de réception technique est configuré dans le contrat X12. (obligatoire) |
| isFunctionalAcknowledgmentExpected | Boolean | Indique si l’accusé de réception fonctionnel est configuré dans le contrat X12. (obligatoire) |
| isMessageFailed | Boolean | Indique si le message X12 a échoué. (obligatoire)|
| gs01 | Chaîne | Code d’identificateur fonctionnel. (facultatif) |
| gs02 | Chaîne | Code de l’expéditeur de l’application. (facultatif) |
| gs03 | Chaîne | Code du destinataire de l’application. (facultatif) |
| gs04 | Chaîne | Date du groupe fonctionnel. (facultatif) |
| gs05 | Chaîne | Heure du groupe fonctionnel. (facultatif) |
| gs07 | Chaîne | Code de l’agence responsable. (facultatif) |
| gs08 | Chaîne | Code identificateur de version/du secteur. (facultatif) |
||||

## <a name="x12-functional-group-acknowledgement-tracking-schema"></a>Schéma de suivi d’accusé de réception de groupe fonctionnel X12

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "functionalGroupControlNumber": "",
      "isaSegment": "",
      "gsSegment": "",
      "respondingfunctionalGroupControlNumber": "",
      "respondingFunctionalGroupId": "",
      "isMessageFailed": "",
      "statusCode": "",
      "processingStatus": "",
      "ak903": "",
      "ak904": "",
      "ak9Segment": ""
   }
}
```

| Propriété | Type | Description |
| --- | --- | --- |
| senderPartnerName | Chaîne | Nom de partenaire de l’expéditeur du message X12. (facultatif) |
| receiverPartnerName | Chaîne | Nom de partenaire du destinataire du message X12. (facultatif) |
| senderQualifier | Chaîne | Qualificateur du partenaire d’envoi. (obligatoire) |
| senderIdentifier | Chaîne | Identificateur du partenaire d’envoi. (obligatoire) |
| receiverQualifier | Chaîne | Qualificateur du partenaire de réception. (obligatoire) |
| receiverIdentifier | Chaîne | Identificateur du partenaire de réception. (obligatoire) |
| agreementName | Chaîne | Nom du contrat X12 dans lequel les messages sont résolus. (facultatif) |
| direction | Enum | Direction du flux de messages (envoi ou réception). (obligatoire) |
| interchangeControlNumber | Chaîne | Numéro de contrôle de l’échange, qui renseigne le côté envoi lors de la réception d’un accusé de réception technique de partenaires. (facultatif) |
| functionalGroupControlNumber | Chaîne | Numéro de contrôle de groupe fonctionnel de l’accusé de réception technique, qui renseigne le côté envoi lors de la réception d’un accusé de réception technique de partenaires. (facultatif) |
| isaSegment | Chaîne | Identique au numéro de contrôle de l’échange, mais renseigné uniquement dans des cas spécifiques. (facultatif) |
| gsSegment | Chaîne | Identique au numéro de contrôle de groupe fonctionnel, mais renseigné uniquement dans des cas spécifiques. (facultatif) |
| respondingfunctionalGroupControlNumber | Chaîne | Numéro de contrôle du groupe fonctionnel d’origine. (facultatif) |
| respondingFunctionalGroupId | Chaîne | Mappé à AK101 dans l’ID de groupe fonctionnel de l’accusé de réception. (facultatif) |
| isMessageFailed | Boolean | Indique si le message X12 a échoué. (obligatoire) |
| statusCode | Enum | Code d’état de l’accusé de réception. Les valeurs autorisées sont **Accepted**, **Rejected** et **AcceptedWithErrors**. (obligatoire) |
| processingStatus | Enum | État de traitement de l’accusé de réception. Les valeurs autorisées sont **Received**, **Generated** ou **Sent**. (obligatoire) |
| ak903 | Chaîne | Nombre maximal de documents informatisés reçus. (facultatif) |
| ak904 | Chaîne | Nombre de documents informatisés acceptés dans le groupe fonctionnel identifié. (facultatif) |
| ak9Segment | Chaîne | Indique si le groupe fonctionnel identifié dans le segment AK1 est accepté ou rejeté et pourquoi. (facultatif) |
|||| 

## <a name="b2b-protocol-tracking-schemas"></a>Schémas de suivi de protocole B2B

Pour plus d’informations sur les schémas de suivi de protocole B2B, consultez :

* [Schémas de suivi AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)   
* [Schémas de suivi B2B personnalisés](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur le [suivi des messages B2B](logic-apps-monitor-b2b-message.md).
* En savoir plus sur le [suivi des messages B2B dans les journaux Azure Monitor](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).
