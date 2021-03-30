---
author: mrbullwinkle
ms.service: application-insights
ms.topic: include
ms.date: 11/09/2018
ms.author: mbullwin
ms.openlocfilehash: 91141e314bf276d9138157c8a9d85d5262ac5907
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "67182989"
---
Kolekcja niestandardowych pomiarów. Użyj tej kolekcji, aby zgłosić nazwaną miarę skojarzoną z elementem telemetrii. Typowe przypadki użycia to:
- rozmiar ładunku telemetrii zależności
- Liczba elementów kolejki przetworzonych przez dane telemetryczne żądania
- czas, przez który klient wykonał krok w ramach telemetrii zdarzeń ukończenia kroku kreatora.

[Pomiary niestandardowe](https://analytics.applicationinsights.io/demo?q=H4sIAAAAAAAAA2WLOw6DMAyGd07hZoLeoRPqyMaGGAL8aiPhGCV2kKoeHsHK%2Bj1myyr8LoiaqfrT%2FkUCzRft4LMl8OUeL3LuLLIx%2BxR%2BIF8%2BtcoiNq2o78vgWuFthQaJ1AeGGxt6UlBwKxa1qQ6EpLhAfQAAAA%3D%3D&timespan=PT24H) można badać w usłudze Application Analytics:

```
customEvents
| where customMeasurements != ""
| summarize avg(todouble(customMeasurements["Completion Time"]) * itemCount)
```

 > [!NOTE]
 > Miary niestandardowe są skojarzone z elementem telemetrii, do którego należą. Są one przedmiotem próbkowania z elementem telemetrii zawierającym te pomiary. Aby śledzić pomiar, który ma wartość niezależną od innych typów telemetrii, użyj danych telemetrycznych [metryk](../articles/azure-monitor/app/api-custom-events-metrics.md).

Maksymalna długość klucza: 150
