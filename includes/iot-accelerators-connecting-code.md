---
title: plik dołączany
description: plik dołączany
services: iot-suite
author: dominicbetts
ms.service: iot-suite
ms.topic: include
ms.date: 09/17/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: c79b6f854dc78670a7eb8a1275c3e2fc46fcdd99
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "67183666"
---
### <a name="code-walkthrough"></a>Przewodnik po kodzie

W tej sekcji opisano niektóre kluczowe fragmenty przykładowego kodu i wyjaśniono, w jaki sposób odnoszą się do akceleratora rozwiązania do monitorowania zdalnego.

Poniższy fragment kodu przedstawia sposób definiowania raportowanych właściwości opisujących możliwości urządzenia. Te właściwości obejmują:

- Lokalizacja urządzenia, aby umożliwić akceleratorowi rozwiązania dodanie urządzenia do mapy.
- Bieżąca wersja oprogramowania układowego.
- Lista metod obsługiwanych przez urządzenie.
- Schemat komunikatów telemetrycznych wysłanych przez urządzenie.

[!code-cpp[Define data structures for Chiller](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=datadefinition "Define data structures for Chiller")]

Przykład zawiera funkcję **serializeToJson** , która serializować tę strukturę danych przy użyciu biblioteki Parson.

Przykład zawiera kilka funkcji wywołania zwrotnego, które drukują informacje do konsoli, gdy klient współdziała z akceleratorem rozwiązania:

- **connection_status_callback**
- **send_confirm_callback**
- **reported_state_callback**
- **device_method_callback**

Poniższy fragment kodu przedstawia funkcję **device_method_callback** . Ta funkcja określa akcję, która ma zostać podjęta po odebraniu wywołania metody z akceleratora rozwiązania. Funkcja otrzymuje odwołanie do struktury danych **chłodzenia** w parametrze **userContextCallback** . Wartość **userContextCallback** jest ustawiana, gdy funkcja wywołania zwrotnego jest konfigurowana w funkcji **Main** :

[!code-cpp[Device method callback](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=devicemethodcallback "Device method callback")]

Gdy Akcelerator rozwiązania wywołuje metodę aktualizacji oprogramowania układowego, próbka deserializacji ładunek JSON i uruchamia wątek w tle, aby ukończyć proces aktualizacji. Poniższy fragment kodu przedstawia **do_firmware_update** , które są uruchamiane w wątku:

[!code-cpp[Firmware update thread](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=firmwareupdate "Firmware update thread")]

Poniższy fragment kodu przedstawia sposób, w jaki klient wysyła komunikat telemetrii do akceleratora rozwiązania. Właściwości komunikatu obejmują schemat wiadomości, aby pomóc akceleratorowi rozwiązania wyświetlić dane telemetryczne na pulpicie nawigacyjnym:

[!code-cpp[Send telemetry](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=sendmessage "Send telemetry")]

Funkcja **Main** w przykładzie:

- Inicjuje i zamyka podsystem zestawu SDK.
- Inicjuje strukturę danych dla **chłodzenia** .
- Wysyła raportowane właściwości do akceleratora rozwiązania.
- Konfiguruje funkcję wywołania zwrotnego metody urządzenia.
- Wysyła symulowane wartości telemetryczne do akceleratora rozwiązania.

[!code-cpp[Main](~/iot-samples-c/samples/solutions/remote_monitoring_client/remote_monitoring.c?name=main "Main")]
