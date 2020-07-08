---
title: Włączanie ciągłej integracji/ciągłego wdrażania przy użyciu wtyczki Jenkins — Azure IoT Edge | Microsoft Docs
description: Azure IoT Edge rozszerzenie Jenkins umożliwia integrowanie IoT Edge zadań związanych z programowaniem i wdrażaniem w istniejącym rozwiązaniu DevOps.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 75ed5f5eda48f10776b854ce4de4d2f855a53634
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "76510247"
---
# <a name="integrate-azure-iot-edge-with-jenkins-pipelines"></a>Integrowanie Azure IoT Edge z potokami Jenkins

Azure IoT Edge ma wbudowaną obsługę usługi Azure DevOps i Azure DevOps Projects, ale również udostępnia wtyczkę do rozszerzenia funkcji DevOps na Jenkins. [Jenkins](https://jenkins.io/) to serwer automatyzacji typu "open source", który używa wtyczek do obsługi wielu typów projektów deweloperskich i wdrożeń, w tym IoT Edge.

Wtyczka Azure IoT Edge dla Jenkins koncentruje się na ciągłej integracji i ciągłego wdrażania. Można utworzyć potok kompilacji i wypychania, który kompiluje moduły i wypycha obrazy kontenera do rejestru kontenerów. Następnie utwórz potok wydania, który wdraża moduły na IoT Edge urządzeniach.

Przed rozpoczęciem korzystania z wtyczki IoT Edge dla Jenkins potrzebne jest centrum IoT Hub na platformie Azure i rejestr kontenerów do przechowywania obrazów kontenerów. Użyj jednostki usługi platformy Azure, aby przyznać uprawnienia współautora Jenkins w usłudze IoT Hub, dzięki czemu wtyczka może tworzyć wdrożenia dla urządzeń IoT Edge.

Jeśli wszystko jest gotowe do rozpoczęcia, Znajdź instalację i użyj szczegółowych informacji dla [wtyczki Azure IoT Edge dla Jenkins](https://plugins.jenkins.io/azure-iot-edge).
