---
title: Architektura IoT Inteligentne zarządzanie zapasami | Dokumenty firmy Microsoft
description: Architektura szablonu IoT Smart Inventory Management dla usługi IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 10/20/2019
ms.custom: mqtt
ms.openlocfilehash: 9e0b4a3c22c1e6008bdce7fecc1d122fe317dd58
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686202"
---
# <a name="architecture-of-iot-central-smart-inventory-management-application-template"></a>Architektura szablonu aplikacji do inteligentnego zarządzania zapasami IoT Central

Partnerzy i klienci mogą korzystać z szablonu aplikacji i postępować zgodnie ze wskazówkami, aby opracować kompleksowe rozwiązania do **zarządzania inteligentnymi zapasami.**

> [!div class="mx-imgBorder"]
> ![inteligentne zarządzanie zapasami](./media/concept-smart-inventory-mgmt-architecture/smart-inventory-management-architecture.png)

1. Zestaw czujników IoT wysyłających dane telemetryczne do urządzenia bramy
2. Urządzenia bramy wysyłające dane telemetryczne i zagregowane szczegółowe informacje do usługi IoT Central
3. Dane są kierowane do żądanej usługi platformy Azure w celu manipulacji
4. Usługi platformy Azure, takie jak ASA lub Usługi Azure Functions, mogą służyć do formatowania strumieni danych i wysyłania ich do żądanych kont magazynu 
5. Przetwarzane dane są przechowywane w magazynie na gorąco dla akcji w czasie zbliżonym do rzeczywistego lub chłodni, aby uzyskać dodatkowe ulepszenia wglądu, które są oparte na analizie uczenia maszynowego lub wsadowej. 
6. Aplikacje logiki mogą być używane do zasilania różnych przepływów pracy biznesowych w aplikacjach biznesowych użytkowników końcowych

## <a name="details"></a>Szczegóły
W poniższej sekcji przedstawiono każdą część architektury koncepcyjnej Połkliwienie telemetrii z oznaczeń identyfikacji radiowej (RFID), niskiej energii Bluetooth (BLE)

## <a name="rfid-tags"></a>Znaczniki RFID
Znaczniki RFID przesyłają dane o elemencie za pośrednictwem fal radiowych. Znaczniki RFID zazwyczaj nie mają baterii, chyba że określono. Znaczniki odbierają energię z fal radiowych generowanych przez czytnik i przekazują sygnał z powrotem do czytnika RFID.

## <a name="ble-tags"></a>Tagi BLE
Energy beacon emituje pakiety danych w regularnych odstępach czasu. Dane beacon są wykrywane przez czytniki BLE lub zainstalowane usługi na smartfonach, a następnie przesyłane do chmury.

## <a name="rfid--ble-readers"></a>Czytniki RFID & BLE
Czytnik RFID konwertuje fale radiowe na bardziej użyteczną formę danych. Informacje zebrane z tagów są następnie przechowywane w lokalnym serwerze brzegowym lub wysyłane do chmury przy użyciu JSON-RPC 2.0 przez MQTT.
Czytnik BLE znany również jako punkty dostępu (AP) są podobne do czytnika RFID. Służy do wykrywania pobliskich sygnałów Bluetooth i przekazywania jego wiadomości do lokalnej usługi Azure IoT Edge lub chmury przy użyciu JSON-RPC 2.0 przez MQTT.
Wiele czytników jest w stanie odczytywać sygnały RFID i sygnały nawigacyjne, a także zapewniać dodatkowe możliwości czujników związane z temperaturą, wilgotnością, akcelerometrem i żyroskopem.

## <a name="azure-iot-edge-gateway"></a>Brama usługi Azure IoT Edge
Serwer usługi Azure IoT Edge zapewnia miejsce do wstępnego przetwarzania tych danych lokalnie przed wysłaniem ich do chmury. Możemy również wdrażać obciążenia w chmurze sztucznej inteligencji, platformy Azure i usług innych firm, logiki biznesowej przy użyciu standardowych kontenerów.

## <a name="device-management-with-iot-central"></a>Zarządzanie urządzeniami za pomocą usługi IoT Central 
Usługa Azure IoT Central to platforma programowa, która upraszcza łączność, konfigurację i zarządzanie urządzeniami IoT. Platforma znacznie zmniejsza obciążenie i koszty zarządzania urządzeniami IoT, operacji i związanych z nimi zmian. Klienci & partnerzy mogą tworzyć kompleksowe rozwiązania dla przedsiębiorstw, aby uzyskać cyfrową pętlę sprzężenia zwrotnego w zarządzaniu zapasami.

## <a name="business-insights--actions-using-data-egress"></a>Wgląd w dane biznesowe & akcji przy użyciu transferu danych wychodzących 
Platforma IoT Central zapewnia zaawansowane opcje rozszerzalności za pośrednictwem ciągłego eksportowania danych (CDE) i interfejsów API. Szczegółowe informacje biznesowe oparte na przetwarzaniu danych telemetrycznych lub nieprzetworzonych danych telemetrycznych są zazwyczaj eksportowane do preferowanej aplikacji biznesowej. Można to osiągnąć za pomocą elementu webhook, magistrali usług, centrum zdarzeń lub magazynu obiektów blob do tworzenia, uczenia i wdrażania modeli uczenia maszynowego, & dalszego wzbogacania szczegółowych informacji.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak wdrożyć [szablon zarządzania inteligentnymi zapasami](./tutorial-iot-central-smart-inventory-management.md)
* Dowiedz się więcej o [szablonach detalicznych IoT Central](./overview-iot-central-retail.md)
* Dowiedz się więcej o IoT Central zapoznaj się z [przeglądem IoT Central](../core/overview-iot-central.md)
