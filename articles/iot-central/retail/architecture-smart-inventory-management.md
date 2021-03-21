---
title: Architektura zarządzania magazynem w usłudze IoT Smart Microsoft Docs
description: Architektura szablonu zarządzania magazynem inteligentnym usługi IoT dla IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: conceptual
ms.date: 10/20/2019
ms.custom: mqtt
ms.openlocfilehash: d7939193b0dad1ed74dccd0ace0b0f0323b17124
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99832644"
---
# <a name="architecture-of-iot-central-smart-inventory-management-application-template"></a>Architektura szablonu IoT Central aplikacji do zarządzania magazynem inteligentnym

Partnerzy i klienci mogą korzystać z szablonu aplikacji i uzyskać wskazówki dotyczące tworzenia kompleksowych rozwiązań do **zarządzania magazynem inteligentnym** .

> [!div class="mx-imgBorder"]
> ![Zarządzanie zapasami inteligentnymi](./media/concept-smart-inventory-mgmt-architecture/smart-inventory-management-architecture.png)

1. Zbiór czujników IoT wysyłających dane telemetryczne do urządzenia bramy
2. Urządzenia bramy wysyłające dane telemetryczne i agregowane szczegółowe dane do IoT Central
3. Dane są kierowane do żądanej usługi platformy Azure w celu manipulowania
4. Za pomocą usług systemu Azure, takich jak ASA lub Azure Functions, można ponownie formatować strumienie danych i wysyłać je do żądanych kont magazynu 
5. Przetworzone dane są przechowywane w magazynie gorącym dla akcji niemal w czasie rzeczywistym lub w chłodnym magazynie w celu uzyskania dodatkowych ulepszeń szczegółowych, które opierają się na analizie ML lub partii. 
6. Logic Apps może służyć do zarządzania różnymi przepływami pracy w aplikacjach dla użytkowników końcowych

## <a name="details"></a>Szczegóły
W poniższej sekcji przedstawiono każdą część pozyskiwania danych telemetrycznych architektury koncepcyjnej z identyfikacji radiowej (RFID), Tagi Bluetooth Low Energy (beli)

## <a name="rfid-tags"></a>Znaczniki RFID
Tagi RFID przesyłają dane dotyczące elementu za za poorednictwem fal radiowych. Tagi RFID zazwyczaj nie mają baterii, chyba że zostanie określony. Tagi odbierają energię z fal radiowych generowanych przez czytnik i przesyłają sygnał z powrotem do czytnika RFID.

## <a name="ble-tags"></a>Tagi dotyczące beli
Sygnał energetyczny emituje pakiety danych w regularnych odstępach czasu. Dane sygnałów są wykrywane przez czytniki beli lub zainstalowane usługi na smartfonach, a następnie przesyłane do chmury.

## <a name="rfid--ble-readers"></a>Czytniki dotyczące &ów RFID
Czytnik RFID konwertuje fale radiowe na bardziej użyteczną formę danych. Informacje zbierane ze znaczników są następnie przechowywane na lokalnym serwerze brzegowym lub wysyłane do chmury przy użyciu JSON-RPC 2,0 przez MQTT.
Czytniki beli znane również jako punkty dostępu (AP) są podobne do czytnika RFID. Służy do wykrywania pobliskich sygnałów Bluetooth i przekazywania wiadomości do lokalnego Azure IoT Edge lub chmury przy użyciu JSON-RPC 2,0 przez MQTT.
Wielu czytników może odczytywać sygnały RFID i sygnałów sygnalizacyjnych oraz zapewniać dodatkową możliwość czujnika odnoszącą się do temperatury, wilgotności, przyspieszeniomierza i żyroskop.

## <a name="azure-iot-edge-gateway"></a>Azure IoT Edge Gateway
Serwer Azure IoT Edge zapewnia wstępne przetwarzanie danych lokalnie przed wysłaniem ich do chmury. Możemy również wdrożyć rozmieszczenie obciążeń w chmurze sztuczną inteligencję, platformę Azure i usługi innych firm, logikę biznesową przy użyciu standardowych kontenerów.

## <a name="device-management-with-iot-central"></a>Zarządzanie urządzeniami za pomocą IoT Central 
Azure IoT Central to platforma programistyczna rozwiązań, która upraszcza łączność, konfigurację i zarządzanie urządzeniami IoT. Platforma znacznie zmniejsza obciążenie i koszty związane z zarządzaniem urządzeniami IoT, operacjami i powiązanymi rozwiązaniami. Klienci & partnerzy mogą tworzyć kompleksowe rozwiązania dla przedsiębiorstw, aby uzyskać pętlę do zarządzania spisem.

## <a name="business-insights--actions-using-data-egress"></a>Szczegółowe informacje biznesowe & działania przy użyciu danych wyjściowych 
Platforma IoT Central zapewnia zaawansowane opcje rozszerzalności za poorednictwem ciągłego eksportowania danych (CDE) i interfejsów API. Szczegółowe informacje biznesowe oparte na przetwarzaniu danych telemetrycznych lub nieprzetworzonej telemetrii są zwykle eksportowane do preferowanych aplikacji biznesowych. Można to osiągnąć za pomocą elementów webhook, Service Bus, Event Hub lub BLOB Storage do kompilowania, uczenia i wdrażania modeli uczenia maszynowego & dodatkowego wzbogacania szczegółowych informacji.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak wdrożyć [szablon zarządzania magazynem inteligentnym](./tutorial-iot-central-smart-inventory-management.md)
* Dowiedz się więcej o [szablonach detalicznych IoT Central](./overview-iot-central-retail.md)
* Dowiedz się więcej o IoT Central zapoznaj się z [omówieniem IoT Central](../core/overview-iot-central.md)
