---
title: Activer CI/CD avec le plug-in Jenkins - Azure IoT Edge | Microsoft Docs
description: L'extension Azure IoT Edge pour Jenkins vous permet d'intégrer des tâches de développement et de déploiement IoT Edge dans votre solution DevOps existante.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 016d1c5d389cf1b9e82194e9d273863da1138d2b
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54830035"
---
# <a name="integrate-azure-iot-edge-with-jenkins-pipelines"></a>Intégrer Azure IoT Edge aux pipelines Jenkins

Outre la prise en charge intégrée d'Azure DevOps et Azure DevOps Projects, Azure IoT Edge fournit également un plug-in qui permet d'étendre les fonctionnalités de DevOps à Jenkins. [Jenkins](https://jenkins.io/) est un serveur d'automatisation open source qui utilise des plug-ins pour prendre en charge de nombreux types de projets de développement et de déploiement, y compris IoT Edge. 

Le plug-in Azure IoT Edge pour Jenkins est axé sur l'intégration et le déploiement continus. Vous pouvez créer un pipeline de création et de transmission de type push qui génère des modules et envoie (push) les images de leurs conteneurs à votre registre de conteneurs. Créez ensuite un pipeline de versions pour déployer les modules sur vos appareils IoT Edge. 

Avant de commencer à utiliser le plug-in IoT Edge pour Jenkins, il vous faut un hub IoT dans Azure et un registre de conteneurs qui stockera les images de vos conteneurs. Utilisez un principal de service Azure pour octroyer des autorisations de contributeur Jenkins à votre hub IoT afin que le plug-in puisse créer des déploiements pour vos appareils IoT Edge. 

Si vous êtes prêt à vous lancer, recherchez les informations d'installation et d'utilisation du [plug-in Azure IoT Edge pour Jenkins](https://plugins.jenkins.io/azure-iot-edge).