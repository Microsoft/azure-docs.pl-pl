---
author: v-dalc
ms.service: databox
ms.author: alkohli
ms.topic: include
ms.date: 02/05/2021
ms.openlocfilehash: b06b91e972fd07543cf02105360cb0400ef6b0f1
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/08/2021
ms.locfileid: "99831557"
---
Aby rozwiązać problemy związane z obliczeniami, użyj odpowiedzi w czasie wykonywania agenta IoT Edge. Poniżej znajduje się lista możliwych odpowiedzi:

* 200 — OK
* 400 — konfiguracja wdrożenia jest źle sformułowana lub nieprawidłowa.
* 417 — urządzenie nie ma ustawionej konfiguracji wdrożenia.
* 412 — wersja schematu w konfiguracji wdrożenia jest nieprawidłowa.
* 406 — urządzenie IoT Edge jest w trybie offline lub nie wysyła raportów o stanie.
* 500 — Wystąpił błąd w czasie wykonywania IoT Edge.

Aby uzyskać więcej informacji, zobacz [IoT Edge Agent](/azure/iot-edge/iot-edge-runtime?view=iotedge-2018-06&preserve-view=true#iot-edge-agent).

Następujący błąd jest związany z usługą IoT Edge w Azure Stack EDGE Pro<!--/ Data Box Gateway--> urządzeniu.

### <a name="compute-modules-have-unknown-status-and-cant-be-used"></a>Moduły obliczeniowe mają nieznany stan i nie można ich użyć

#### <a name="error-description"></a>Opis błędu

Wszystkie moduły na urządzeniu pokazują nieznany stan i nie można ich użyć. Nieznany stan utrzymuje się po ponownym uruchomieniu.<!--Original Support ticket relates to trying to deploy a container app on a Hub. Based on the work item, I assume the error description should not be that specific, and that the error applies to Azure Stack Edge Devices, which is the focus of this troubleshooting.-->

#### <a name="suggested-solution"></a>Sugerowane rozwiązanie

Usuń usługę IoT Edge, a następnie ponownie Wdróż moduły. Aby uzyskać więcej informacji, zobacz [usuwanie usługi IoT Edge](../articles/databox-online/azure-stack-edge-j-series-manage-compute.md#remove-iot-edge-service).
