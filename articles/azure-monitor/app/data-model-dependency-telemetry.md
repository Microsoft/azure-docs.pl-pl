---
title: Model danych zależności Application Insights Azure Monitor
description: Application Insights model danych dla telemetrii zależności
ms.topic: conceptual
ms.date: 04/17/2017
ms.reviewer: sergkanz
ms.openlocfilehash: ba0d848904d1ba885dc53e2941953d8dfb4864cf
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "77671923"
---
# <a name="dependency-telemetry-application-insights-data-model"></a>Telemetria zależności: Application Insights model danych

Telemetria zależności (w [Application Insights](../../azure-monitor/app/app-insights-overview.md)) reprezentuje interakcję monitorowanego składnika ze składnikiem zdalnym, takim jak SQL lub punkt końcowy HTTP.

## <a name="name"></a>Nazwa

Nazwa polecenia zainicjowanego z tym wywołaniem zależności. Niska wartość kardynalności. Przykładami są nazwa procedury przechowywanej i szablon ścieżki URL.

## <a name="id"></a>ID

Identyfikator wystąpienia wywołania zależności. Używany do korelacji z elementem żądania telemetrii, odpowiadającym temu wywołaniu zależności. Aby uzyskać więcej informacji, zobacz stronę [korelacji](../../azure-monitor/app/correlation.md) .

## <a name="data"></a>Dane

Polecenie zainicjowane przez to wywołanie zależności. Przykłady to instrukcja SQL i adres URL protokołu HTTP ze wszystkimi parametrami zapytania.

## <a name="type"></a>Typ

Nazwa typu zależności. Niska wartość kardynalności logicznego grupowania zależności i interpretacji innych pól, takich jak CommandName i resultCode. Przykłady to SQL, Azure Table i HTTP.

## <a name="target"></a>Środowisko docelowe

Lokacja docelowa wywołania zależności. Przykłady to nazwa serwera, adres hosta. Aby uzyskać więcej informacji, zobacz stronę [korelacji](../../azure-monitor/app/correlation.md) .

## <a name="duration"></a>Czas trwania

Czas trwania żądania w formacie: `DD.HH:MM:SS.MMMMMM` . Musi być krótszy niż `1000` Liczba dni.

## <a name="result-code"></a>Kod wyniku

Kod wyniku wywołania zależności. Przykłady to kod błędu SQL i kod stanu HTTP.

## <a name="success"></a>Powodzenie

Wskazanie powodzenia lub nieudanych wywołań.

## <a name="custom-properties"></a>Właściwości niestandardowe

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Pomiary niestandardowe

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]


## <a name="next-steps"></a>Następne kroki

- Skonfiguruj Śledzenie zależności dla [platformy .NET](../../azure-monitor/app/asp-net-dependencies.md).
- Skonfiguruj Śledzenie zależności dla [środowiska Java](../../azure-monitor/app/java-agent.md).
- [Napisz niestandardową telemetrię zależności](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency)
- Zobacz [model danych](data-model.md) dla typów Application Insights i modelu danych.
- Sprawdź [platformy](../../azure-monitor/app/platforms.md) obsługiwane przez Application Insights.
