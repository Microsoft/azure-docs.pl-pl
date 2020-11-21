---
title: IoT Edge Nawigacja i historia wersji — Azure IoT Edge
description: Poznaj nowości w programie IoT Edge z informacjami o nowych funkcjach i możliwościach w najnowszych wersjach.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/08/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: cdb84f817f63e6401d17f18319e161f4c3477293
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024657"
---
# <a name="azure-iot-edge-versions-and-release-notes"></a>Azure IoT Edge wersje i informacje o wersji

Azure IoT Edge to produkt zbudowany z projektu IoT Edge typu open source hostowanego w witrynie GitHub. Wszystkie nowe wersje są udostępniane w [projekcie Azure IoT Edge](https://github.com/Azure/azure-iotedge). Udziały i raporty o usterkach można wprowadzać w [projekcie IoT Edge Open Source](https://github.com/Azure/iotedge).

## <a name="documented-versions"></a>Udokumentowane wersje

Dokumentacja IoT Edge w tej witrynie jest dostępna dla dwóch różnych wersji produktu, dzięki czemu można wybrać zawartość, która ma zastosowanie do środowiska IoT Edge. Obecnie dwie obsługiwane wersje to:

* **IoT Edge 1.0.10** obejmuje wszystkie funkcje i możliwości, korzystając z najnowszej, ogólnie dostępnej wersji: [1.0.10](https://github.com/Azure/azure-iotedge/releases/tag/1.0.10).
* **IoT Edge 1,2 (wersja zapoznawcza)** zawiera dodatkową zawartość dla funkcji i możliwości dostępnych w najnowszej wersji zapoznawczej: [1,2 — RC1](https://github.com/Azure/azure-iotedge/releases/tag/1.2.0-rc1)
  * Gdy IoT Edge 1,2 jest w wersji zapoznawczej, należy zainstalować wersje Release Candidate. Aby uzyskać więcej informacji, zobacz [Instalacja w trybie offline lub w określonej wersji](how-to-install-iot-edge.md?tabs=linux#offline-or-specific-version-installation).

## <a name="version-history"></a>Historia wersji

Ta tabela zawiera informacje o historii najnowszej wersji IoT Edge wydań pakietów oraz informacje o aktualizacjach dokumentacji wykonanych dla każdej wersji.

| Informacje o wersji i zasoby | Typ | Date | Najważniejsze informacje |
| ------------------------ | ---- | ---- | ---------- |
| [1,2 — RC1](https://github.com/Azure/azure-iotedge/releases/tag/1.2.0-rc1) | Wersja zapoznawcza | Listopad 2020 r. | [IoT Edge urządzeń za bramami](how-to-connect-downstream-iot-edge-device.md?view=iotedge-2020-11&preserve-view=true)<br>[IoT Edge brokera MQTT](how-to-publish-subscribe.md?view=iotedge-2020-11&preserve-view=true) |
| [1.0.10](https://github.com/Azure/azure-iotedge/releases/tag/1.0.10) | Stable | Październik 2020 r. | [Metoda Direct UploadSupportBundle](how-to-retrieve-iot-edge-logs.md#upload-support-bundle-diagnostics)<br>[Przekazywanie metryk środowiska uruchomieniowego](how-to-access-built-in-metrics.md)<br>[Priorytet trasy i czas wygaśnięcia](module-composition.md#priority-and-time-to-live)<br>[Kolejność uruchamiania modułu](module-composition.md#configure-modules)<br>[Inicjowanie obsługi ręcznej X. 509](how-to-manual-provision-x509.md) |
| [1.0.9](https://github.com/Azure/azure-iotedge/releases/tag/1.0.9) | Stable | Marzec 2020 r. | [Inicjowanie obsługi administracyjnej X. 509 za pomocą DPS](how-to-auto-provision-x509-certs.md)<br>[Metoda Direct RestartModule](how-to-edgeagent-direct-method.md#restart-module)<br>[support — polecenie pakietu](troubleshoot.md#gather-debug-information-with-support-bundle-command) |

## <a name="next-steps"></a>Następne kroki

* [Wyświetl wszystkie Azure IoT Edge wersje](https://github.com/Azure/azure-iotedge/releases)
* [Tworzenie i przeglądanie żądań funkcji na forum opinii](https://feedback.azure.com/forums/907045-azure-iot-edge)