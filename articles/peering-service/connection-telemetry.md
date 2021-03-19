---
title: Telemetria połączenia usługi Komunikacja równorzędna Azure
description: Informacje o telemetrii połączenia usługi Komunikacja równorzędna Microsoft Azure
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 0e54b20cc6aefa69226c24f557ae02166dc4b4b6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "84872008"
---
# <a name="peering-service-connection-telemetry"></a>Telemetria połączenia usługi Komunikacja równorzędna

Dane telemetryczne połączenia udostępniają szczegółowe informacje zebrane na potrzeby łączności między lokalizacją klienta a siecią firmy Microsoft. Klienci mogą uzyskać dane telemetryczne dla połączenia usługi Komunikacja równorzędna Azure przez zarejestrowanie połączenia w Azure Portal. Ta funkcja udostępnia prefiks zabezpieczeń i szczegółowe informacje o wydajności sieci.


Dane telemetryczne połączenia składają się z następujących zakresów:

### <a name="latency-measurement"></a>Pomiar opóźnienia

 Opóźnienie jest mierzone od klienta do okna podręcznego przeglądarki Microsoft Edge dla zarejestrowanych prefiksów w ramach usługi komunikacji równorzędnej.

### <a name="route-prefix-monitoring-and-protection"></a>Monitorowanie i ochrona prefiksów tras

Ścieżki routingu są monitorowane pod kątem podejrzanych działań, które następnie są przechwytywane w dziennikach zdarzeń. Na przykład dzienniki zdarzeń są tworzone dla niektórych z następujących czynników:

- Przejmowanie prefiksu
- Cofnięcie prefiksu
- Przecieki tras

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o połączeniu z usługą komunikacji równorzędnej, zobacz [nawiązywanie połączenia z usługą komunikacji równorzędnej](connection.md).
- Aby dołączyć połączenie z usługą komunikacji równorzędnej, zobacz Dołączanie [modelu usług komunikacji równorzędnej](onboarding-model.md).
- Aby mierzyć dane telemetryczne, zobacz [mierzenie danych telemetrycznych połączenia](measure-connection-telemetry.md).
