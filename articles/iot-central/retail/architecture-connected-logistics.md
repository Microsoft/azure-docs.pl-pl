---
title: Architektura zagadnień związanych z usługą IoT Microsoft Docs
description: Architektura szablonu aplikacji logistycznej połączonej IoT dla IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 10/20/2019
ms.custom: mqtt
ms.openlocfilehash: 8925b98269b67bfb8a96cb057982ee4e396f17ed
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "81686226"
---
# <a name="architecture-of-iot-central-connected-logistics-application-template"></a>Architektura szablonu IoT Central połączonej aplikacji logistycznej



Partnerzy & klienci mogą używać szablonu aplikacji & następujące wskazówki umożliwiają tworzenie kompleksowych, **połączonych rozwiązań logistycznych**.

> [!div class="mx-imgBorder"]
> ![połączony pulpit nawigacyjny logistyki](./media/concept-connected-logistics-architecture/connected-logistics-architecture.png)

1. Zestaw tagów IoT wysyłających dane telemetryczne do urządzenia bramy
2. Urządzenia bramy wysyłające dane telemetryczne i agregowane szczegółowe dane do IoT Central
3. Dane są kierowane do żądanej usługi platformy Azure w celu manipulowania
4. Za pomocą usług systemu Azure, takich jak ASA lub Azure Functions, można ponownie formatować strumienie danych i wysyłać je do żądanych kont magazynu 
5. Różne biznesowe przepływy pracy mogą być obsługiwane przez aplikacje biznesowe użytkowników końcowych

## <a name="details"></a>Szczegóły
W poniższej sekcji opisano każdą część pozyskiwania danych telemetrycznych architektury koncepcyjnej ze znaczników IoT & bram

## <a name="iot-tags"></a>Tagi IoT
Tagi IoT zapewniają fizyczne, otoczenie i czujniki środowiskowe, takie jak temperatura, wilgotność, wstrząsy, nachylenie &jasne. Tagi IoT zazwyczaj łączą się z urządzeniem bramy za poorednictwem Zigbee (802.15.4). Tagi są mniej kosztowne czujniki; Dzięki temu można je odrzucić na końcu typowej podróży logistycznej, aby uniknąć problemów z odwrotną logistyką.

## <a name="gateway"></a>Brama
Bramy mogą również pełnić rolę tagów IoT z możliwością ich detekcji. Brama umożliwia nadrzędną łączność w chmurze Azure IoT (MQTT) przy użyciu kanałów komórkowych i sieci Wi-Fi.  Tryby połączenia Bluetooth, NFC i 802.15.4 Wireless sensor Network (WSN) są używane do komunikacji podrzędnej ze znacznikami IoT. Bramy zapewniają kompleksowe bezpieczeństwo łączności w chmurze, parowanie tagów IoT, agregację danych czujników, przechowywanie danych i możliwość konfigurowania progów alarmów.

## <a name="device-management-with-iot-central"></a>Zarządzanie urządzeniami za pomocą IoT Central 
Azure IoT Central to platforma programistyczna rozwiązań, która upraszcza łączność, konfigurację i zarządzanie urządzeniami IoT. Platforma znacznie zmniejsza obciążenie i koszty związane z zarządzaniem urządzeniami IoT, operacjami i powiązanymi rozwiązaniami. Klienci & partnerzy mogą tworzyć kompleksowe rozwiązania dla przedsiębiorstw, aby uzyskać pętlę do przesyłania opinii cyfrowych w ramach logistyki.

## <a name="business-insights-and-actions-using-data-egress"></a>Szczegółowe informacje biznesowe i akcje przy użyciu danych wyjściowych 
Platforma IoT Central zapewnia zaawansowane opcje rozszerzalności za poorednictwem ciągłego eksportowania danych (CDE) i interfejsów API. Szczegółowe informacje biznesowe oparte na przetwarzaniu danych telemetrycznych lub nieprzetworzonej telemetrii są zwykle eksportowane do preferowanych aplikacji biznesowych. Można to osiągnąć za pomocą elementów webhook, Service Bus, Event Hub lub BLOB Storage do kompilowania, uczenia i wdrażania modeli uczenia maszynowego & dodatkowego wzbogacania szczegółowych informacji.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak wdrożyć [szablon rozwiązanych rozwiązań logistycznych](./tutorial-iot-central-connected-logistics.md)
* Dowiedz się więcej o [szablonach detalicznych IoT Central](./overview-iot-central-retail.md)
* Dowiedz się więcej o IoT Central zapoznaj się z [omówieniem IoT Central](../core/overview-iot-central.md)
