---
title: IoT Edge Nawigacja i historia wersji — Azure IoT Edge
description: Poznaj nowości w programie IoT Edge z informacjami o nowych funkcjach i możliwościach w najnowszych wersjach.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/07/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 1d3473e975e7c69a83c359b040a2de0defaac69b
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107310477"
---
# <a name="azure-iot-edge-versions-and-release-notes"></a>Azure IoT Edge wersje i informacje o wersji

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Azure IoT Edge to produkt zbudowany z projektu IoT Edge typu open source hostowanego w witrynie GitHub. Wszystkie nowe wersje są udostępniane w [projekcie Azure IoT Edge](https://github.com/Azure/azure-iotedge). Udziały i raporty o usterkach można wprowadzać w [projekcie IoT Edge Open Source](https://github.com/Azure/iotedge).

## <a name="documented-versions"></a>Udokumentowane wersje

Dokumentacja IoT Edge w tej witrynie jest dostępna dla dwóch różnych wersji produktu, dzięki czemu można wybrać zawartość, która ma zastosowanie do środowiska IoT Edge. Obecnie dwie obsługiwane wersje to:

* **IoT Edge 1,2** zawiera dodatkową zawartość dla nowych funkcji i możliwości, które znajdują się w najnowszej stabilnej wersji.
* **IoT Edge 1,1 (LTS)** to pierwsza długoterminowa pomoc techniczna (LTS) IoT Edge. Dokumentacja tej wersji obejmuje wszystkie funkcje i możliwości ze wszystkich poprzednich wersji do 1,1. Ta wersja dokumentacji będzie stabilna przez obsługiwany okres istnienia wersji 1,1 i nie będzie odzwierciedlać nowych funkcji opublikowanych w nowszych wersjach.

Więcej informacji o wersjach IoT Edge można znaleźć w temacie [Azure IoT Edge obsługiwane systemy](support.md).

## <a name="version-history"></a>Historia wersji

Ta tabela zawiera informacje o historii najnowszej wersji IoT Edge wydań pakietów oraz informacje o aktualizacjach dokumentacji wykonanych dla każdej wersji.

| Informacje o wersji i zasoby | Typ | Date (Data) | Najważniejsze informacje |
| ------------------------ | ---- | ---- | ---------- |
| [1.2](https://github.com/Azure/azure-iotedge/releases/tag/1.2.0) | Stable | Kwiecień 2021 | [IoT Edge urządzeń za bramami](how-to-connect-downstream-iot-edge-device.md?view=iotedge-2020-11&preserve-view=true)<br>[IoT Edge Broker MQTT (wersja zapoznawcza)](how-to-publish-subscribe.md?view=iotedge-2020-11&preserve-view=true)<br>Wprowadzono nowe pakiety IoT Edge z nowymi krokami instalacji i konfiguracji. Aby uzyskać więcej informacji, zobacz [Aktualizacja z 1,0 lub 1,1 do 1,2](how-to-update-iot-edge.md#special-case-update-from-10-or-11-to-12)
| [1.1](https://github.com/Azure/azure-iotedge/releases/tag/1.1.0) | Obsługa długoterminowa (LTS, Long Term Support) | Luty 2021 r. | [Długoterminowe planowanie i obsługiwane aktualizacje systemów](support.md) |
| [1.0.10](https://github.com/Azure/azure-iotedge/releases/tag/1.0.10) | Stable | Październik 2020 r. | [Metoda Direct UploadSupportBundle](how-to-retrieve-iot-edge-logs.md#upload-support-bundle-diagnostics)<br>[Przekazywanie metryk środowiska uruchomieniowego](how-to-access-built-in-metrics.md)<br>[Priorytet trasy i czas wygaśnięcia](module-composition.md#priority-and-time-to-live)<br>[Kolejność uruchamiania modułu](module-composition.md#configure-modules)<br>[Inicjowanie obsługi ręcznej X. 509](how-to-register-device.md) |
| [1.0.9](https://github.com/Azure/azure-iotedge/releases/tag/1.0.9) | Stable | Marzec 2020 r. | [Inicjowanie obsługi administracyjnej X. 509 za pomocą DPS](how-to-auto-provision-x509-certs.md)<br>[Metoda Direct RestartModule](how-to-edgeagent-direct-method.md#restart-module)<br>[support — polecenie pakietu](troubleshoot.md#gather-debug-information-with-support-bundle-command) |

## <a name="next-steps"></a>Następne kroki

* [Wyświetl wszystkie Azure IoT Edge wersje](https://github.com/Azure/azure-iotedge/releases)
* [Tworzenie i przeglądanie żądań funkcji na forum opinii](https://feedback.azure.com/forums/907045-azure-iot-edge)