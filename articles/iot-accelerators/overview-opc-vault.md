---
title: Présentation d’OPC Vault - Azure | Microsoft Docs
description: Cet article fournit une vue d’ensemble d’OPC Vault. Il peut configurer, inscrire et gérer le cycle de vie des certificats pour les applications OPC UA dans le cloud.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 66a322d4f60d9553a68207136ae609c1f9b50dbc
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826216"
---
# <a name="what-is-opc-vault"></a>Présentation d’OPC Vault

OPC Vault est un microservice qui peut configurer, inscrire et gérer le cycle de vie des certificats pour les applications serveur et clientes OPC UA dans le cloud. Cet article décrit les cas d’utilisation simples d’OPC Vault.

## <a name="certificate-management"></a>Gestion des certificats

Par exemple, une entreprise de fabrication doit connecter sa machine serveur OPC UA à son application cliente nouvellement générée. Quand cette entreprise effectue l’accès initial de la machine serveur, un message d’erreur est immédiatement visible sur l’application serveur OPC UA pour indiquer que l’application cliente n’est pas sécurisée. Ce mécanisme est intégré à la machine serveur OPC UA pour empêcher tout accès non autorisé à l’application, prévenant un piratage vicieux au niveau de l’atelier.

## <a name="application-security-management"></a>Gestion de la sécurité des applications
Un professionnel de la sécurité utilise le microservice OPC Vault pour permettre facilement au serveur OPC UA de communiquer avec n’importe quelle application cliente, étant donné qu’OPC Vault a toutes les fonctions de registre de certificat, de stockage et de gestion du cycle de vie. Le serveur OPC UA étant connecté de manière sécurisée, il peut communiquer avec l’application cliente nouvellement générée.

## <a name="the-complete-opc-vault-architecture"></a>Architecture d’OPC Vault complète
Le diagramme suivant illustre l’architecture d’OPC Vault complète.

![Architecture d’OPC Vault](media/overview-opc-vault-architecture/opc-vault.png)

## <a name="next-steps"></a>Étapes suivantes

OPC Vault et ses utilisations n’ayant plus de secrets pour vous, voici l’étape suivante que nous suggérons :

> [!div class="nextstepaction"]
> [Architecture d’OPC Vault](overview-opc-vault-architecture.md)
