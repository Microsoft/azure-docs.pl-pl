---
title: Rozwiązywanie problemów z usługą Azure IoT Hub błąd 403002 IoTHubQuotaExceeded
description: Dowiedz się, jak naprawić błąd 403002 IoTHubQuotaExceeded
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 3521933baa8dc328910fbacd8b1b6768832fa5f1
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061319"
---
# <a name="403002-iothubquotaexceeded"></a>403002 IoTHubQuotaExceeded

W tym artykule opisano przyczyny i rozwiązania **403002 błędów IoTHubQuotaExceeded** .

## <a name="symptoms"></a>Objawy

Wszystkie żądania do IoT Hub kończą się niepowodzeniem z błędem  **403002 IoTHubQuotaExceeded**. W Azure Portal nie można załadować listy urządzeń Centrum IoT Hub.

## <a name="cause"></a>Przyczyna

Przekroczono dzienny limit przydziału komunikatów dla Centrum IoT. 

## <a name="solution"></a>Rozwiązanie

[Uaktualnij lub Zwiększ liczbę jednostek w centrum IoT](iot-hub-upgrade.md) lub poczekaj na następny dzień UTC, aby odświeżyć dzienny limit przydziału.

## <a name="next-steps"></a>Następne kroki

* Aby zrozumieć, w jaki sposób operacje są wliczane do limitu przydziału, takich jak zapytania dwuosiowe i metody bezpośrednie, zobacz temat [Omówienie cen IoT Hub](iot-hub-devguide-pricing.md#charges-per-operation)
* Aby skonfigurować monitorowanie dla dziennego użycia przydziału, skonfiguruj alert z *łączną liczbą używanych komunikatów*. Aby uzyskać instrukcje krok po kroku, zobacz [Konfigurowanie metryk i alertów za pomocą IoT Hub](tutorial-use-metrics-and-diags.md#set-up-metrics)