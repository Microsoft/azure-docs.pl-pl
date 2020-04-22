---
title: Architektura IoT Connected logistyka | Dokumenty firmy Microsoft
description: Architektura szablonu aplikacji IoT Connected Logistics dla IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 10/20/2019
ms.custom: mqtt
ms.openlocfilehash: 8925b98269b67bfb8a96cb057982ee4e396f17ed
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686226"
---
# <a name="architecture-of-iot-central-connected-logistics-application-template"></a>Architektura szablonu połączonej aplikacji logistycznej IoT Central



Partnerzy & klient mogą korzystać z szablonu aplikacji & następujących wskazówek w celu opracowania **kompleksowych rozwiązań logistycznych połączonych.**

> [!div class="mx-imgBorder"]
> ![połączony pulpit nawigacyjny logistyki](./media/concept-connected-logistics-architecture/connected-logistics-architecture.png)

1. Zestaw znaczników IoT wysyłających dane telemetryczne do urządzenia bramy
2. Urządzenia bramy wysyłające dane telemetryczne i zagregowane szczegółowe informacje do usługi IoT Central
3. Dane są kierowane do żądanej usługi platformy Azure w celu manipulacji
4. Usługi platformy Azure, takie jak ASA lub Usługi Azure Functions, mogą służyć do formatowania strumieni danych i wysyłania ich do żądanych kont magazynu 
5. Różne przepływy pracy biznesowej mogą być obsługiwane przez aplikacje biznesowe użytkowników końcowych

## <a name="details"></a>Szczegóły
W poniższej sekcji przedstawiono każdą część architektury koncepcyjnej Pozyskiwania telemetrii ze znaczników IoT & bram

## <a name="iot-tags"></a>Znaczniki IoT
Znaczniki IoT zapewniają funkcje czujników fizycznych, otoczenia i środowiska, takich jak temperatura, wilgotność, wstrząsy, pochylenie &światło. Tagi IoT zazwyczaj łączą się z urządzeniem bramy za pośrednictwem Zigbee (802.15.4). Tagi są tańsze czujniki; można je wyrzucić pod koniec typowej podróży logistycznej, aby uniknąć wyzwań związanych z logistyką odwrotną.

## <a name="gateway"></a>Brama
Bramy mogą również działać jako znaczniki IoT z ich możliwości wykrywania otoczenia. Brama umożliwia łączność w chmurze Azure IoT (MQTT) za pomocą kanałów komórkowych, Wi-Fi.  Tryby Bluetooth, NFC i 802.15.4 Wireless Sensor Network (WSN) służą do dalszej komunikacji ze znacznikami IoT. Bramy zapewniają kompleksową bezpieczną łączność w chmurze, parowanie tagów IoT, agregację danych czujników, przechowywanie danych i możliwość konfigurowania progów alarmowych.

## <a name="device-management-with-iot-central"></a>Zarządzanie urządzeniami za pomocą usługi IoT Central 
Usługa Azure IoT Central to platforma programowa, która upraszcza łączność, konfigurację i zarządzanie urządzeniami IoT. Platforma znacznie zmniejsza obciążenie i koszty zarządzania urządzeniami IoT, operacji i związanych z nimi zmian. Klienci & partnerzy mogą tworzyć kompleksowe rozwiązania dla przedsiębiorstw, aby osiągnąć cyfrową pętlę sprzężenia zwrotnego w logistyce.

## <a name="business-insights-and-actions-using-data-egress"></a>Wgląd w dane biznesowe i działania przy użyciu transferu danych wychodzących 
Platforma IoT Central zapewnia zaawansowane opcje rozszerzalności za pośrednictwem ciągłego eksportowania danych (CDE) i interfejsów API. Szczegółowe informacje biznesowe oparte na przetwarzaniu danych telemetrycznych lub nieprzetworzonych danych telemetrycznych są zazwyczaj eksportowane do preferowanej aplikacji biznesowej. Można to osiągnąć za pomocą elementu webhook, magistrali usług, centrum zdarzeń lub magazynu obiektów blob do tworzenia, uczenia i wdrażania modeli uczenia maszynowego, & dalszego wzbogacania szczegółowych informacji.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak wdrożyć [szablon połączonego rozwiązania logistycznego](./tutorial-iot-central-connected-logistics.md)
* Dowiedz się więcej o [szablonach detalicznych IoT Central](./overview-iot-central-retail.md)
* Dowiedz się więcej o IoT Central zapoznaj się z [przeglądem IoT Central](../core/overview-iot-central.md)
