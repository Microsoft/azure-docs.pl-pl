---
title: Model danych Application Insights platformy Azure — dane telemetryczne śledzenia
description: Application Insights model danych na potrzeby telemetrii śledzenia
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 8fea4bbf590816b2ef168a2ed16f197389ee282e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87320548"
---
# <a name="trace-telemetry-application-insights-data-model"></a>Dane telemetryczne śledzenia: Application Insights model danych

Dane telemetryczne śledzenia (w [Application Insights](./app-insights-overview.md)) reprezentują `printf` instrukcje śledzenia stylów, które są przeszukiwane przez tekst. `Log4Net`, `NLog` i inne wpisy pliku dziennika na podstawie tekstu są tłumaczone na wystąpienia tego typu. Ślad nie ma pomiarów jako rozszerzalności.

## <a name="message"></a>Komunikat

Komunikat śledzenia.

Maksymalna długość: 32768 znaków

## <a name="severity-level"></a>Poziom ważności

Poziom ważności śledzenia. Wartość może być `Verbose` , `Information` ,,, `Warning` `Error` `Critical` .

## <a name="custom-properties"></a>Właściwości niestandardowe

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>Następne kroki

- [Poznaj dzienniki śledzenia .NET w Application Insights](./asp-net-trace-logs.md).
- [Poznaj dzienniki śledzenia środowiska Java w Application Insights](./java-trace-logs.md).
- Zobacz [model danych](data-model.md) dla typów Application Insights i modelu danych.
- [Napisz niestandardowe dane telemetryczne śledzenia](./api-custom-events-metrics.md#tracktrace)
- Sprawdź [platformy](./platforms.md) obsługiwane przez Application Insights.

