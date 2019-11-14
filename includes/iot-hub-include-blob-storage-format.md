---
title: Fichier Include
description: Fichier Include
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/15/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 27a254b664e2035cc66109f7a473a3e9ef4d730e
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73612057"
---
<!-- This is the note explaining about the avro and json formats when routing to blob storage. -->
> [!NOTE]
> Les données peuvent être écrites dans un espace de stockage d’objets blob au format [Apache Avro](https://avro.apache.org/), qui est la valeur par défaut, ou JSON (préversion). 
>    
> La fonctionnalité permettant d’encoder au format JSON est en préversion dans toutes les régions où IoT Hub est disponible, à l’exception des régions USA Est, USA Ouest et Europe Ouest. Le format d’encodage peut être défini uniquement au moment de configurer le point de terminaison du stockage d’objets blob. Le format ne peut pas être modifié pour un point de terminaison déjà configuré. Quand vous utilisez l’encodage JSON, vous devez définir contentType sur JSON et contentEncoding sur UTF-8 dans les propriétés système de message. 
>
> Pour plus d’informations sur l’utilisation d’un point de terminaison de stockage d’objets blob, voir les [recommandations en matière de routage vers un espace de stockage](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#azure-storage).
>