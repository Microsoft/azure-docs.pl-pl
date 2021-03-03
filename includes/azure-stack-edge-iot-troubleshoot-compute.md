---
author: v-dalc
ms.service: databox
ms.author: alkohli
ms.topic: include
ms.date: 02/05/2021
ms.openlocfilehash: ad981264a99bd48e27f745a789ebe857b7f17d80
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101750554"
---
Aby rozwiązać problemy związane z obliczeniami, użyj odpowiedzi w czasie wykonywania agenta IoT Edge. Poniżej znajduje się lista możliwych odpowiedzi:

* 200 — OK
* 400 — konfiguracja wdrożenia jest źle sformułowana lub nieprawidłowa.
* 417 — urządzenie nie ma ustawionej konfiguracji wdrożenia.
* 412 — wersja schematu w konfiguracji wdrożenia jest nieprawidłowa.
* 406 — urządzenie IoT Edge jest w trybie offline lub nie wysyła raportów o stanie.
* 500 — Wystąpił błąd w czasie wykonywania IoT Edge.

Aby uzyskać więcej informacji, zobacz [IoT Edge Agent](../articles/iot-edge/iot-edge-runtime.md?preserve-view=true&view=iotedge-2018-06#iot-edge-agent).

Następujący błąd jest związany z usługą IoT Edge w Azure Stack EDGE Pro<!--/ Data Box Gateway--> urządzeniu.

### <a name="compute-modules-have-unknown-status-and-cant-be-used"></a>Moduły obliczeniowe mają nieznany stan i nie można ich użyć

#### <a name="error-description"></a>Opis błędu

Wszystkie moduły na urządzeniu pokazują nieznany stan i nie można ich użyć. Nieznany stan utrzymuje się po ponownym uruchomieniu.<!--Original Support ticket relates to trying to deploy a container app on a Hub. Based on the work item, I assume the error description should not be that specific, and that the error applies to Azure Stack Edge Devices, which is the focus of this troubleshooting.-->

#### <a name="suggested-solution"></a>Sugerowane rozwiązanie

Usuń usługę IoT Edge, a następnie ponownie Wdróż moduły. Aby uzyskać więcej informacji, zobacz [usuwanie usługi IoT Edge](../articles/databox-online/azure-stack-edge-j-series-manage-compute.md#remove-iot-edge-service).