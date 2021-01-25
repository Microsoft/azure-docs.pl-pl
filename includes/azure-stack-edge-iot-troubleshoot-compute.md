---
author: v-dalc
ms.service: databox
ms.author: alkohli
ms.topic: include
ms.date: 01/21/2021
ms.openlocfilehash: 3defa62c55bb5ab042ade816f611ea45b39a0117
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/25/2021
ms.locfileid: "98761540"
---
Aby rozwiązać problemy związane z obliczeniami, użyj odpowiedzi w czasie wykonywania agenta IoT Edge. Poniżej znajduje się lista możliwych odpowiedzi:

* 200 — OK
* 400 — konfiguracja wdrożenia jest źle sformułowana lub nieprawidłowa.
* 417 — urządzenie nie ma ustawionej konfiguracji wdrożenia.
* 412 — wersja schematu w konfiguracji wdrożenia jest nieprawidłowa.
* 406 — urządzenie IoT Edge jest w trybie offline lub nie wysyła raportów o stanie.
* 500 — Wystąpił błąd w czasie wykonywania IoT Edge.

Aby uzyskać więcej informacji, zobacz [IoT Edge Agent](/azure/iot-edge/iot-edge-runtime?view=iotedge-2018-06&preserve-view=true#iot-edge-agent).