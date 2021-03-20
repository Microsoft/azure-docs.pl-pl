---
title: Model danych zależności Application Insights Azure Monitor
description: Application Insights model danych dla telemetrii zależności
ms.topic: conceptual
ms.date: 04/17/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 0f9fc96479569c3411024068ed614d422035ab17
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87315975"
---
# <a name="dependency-telemetry-application-insights-data-model"></a>Telemetria zależności: Application Insights model danych

Telemetria zależności (w [Application Insights](./app-insights-overview.md)) reprezentuje interakcję monitorowanego składnika ze składnikiem zdalnym, takim jak SQL lub punkt końcowy HTTP.

## <a name="name"></a>Nazwa

Nazwa polecenia zainicjowanego z tym wywołaniem zależności. Niska wartość kardynalności. Przykładami są nazwa procedury przechowywanej i szablon ścieżki URL.

## <a name="id"></a>ID (Identyfikator)

Identyfikator wystąpienia wywołania zależności. Używany do korelacji z elementem żądania telemetrii, odpowiadającym temu wywołaniu zależności. Aby uzyskać więcej informacji, zobacz stronę [korelacji](./correlation.md) .

## <a name="data"></a>Dane

Polecenie zainicjowane przez to wywołanie zależności. Przykłady to instrukcja SQL i adres URL protokołu HTTP ze wszystkimi parametrami zapytania.

## <a name="type"></a>Typ

Nazwa typu zależności. Niska wartość kardynalności logicznego grupowania zależności i interpretacji innych pól, takich jak CommandName i resultCode. Przykłady to SQL, Azure Table i HTTP.

## <a name="target"></a>Cel

Lokacja docelowa wywołania zależności. Przykłady to nazwa serwera, adres hosta. Aby uzyskać więcej informacji, zobacz stronę [korelacji](./correlation.md) .

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

- Skonfiguruj Śledzenie zależności dla [platformy .NET](./asp-net-dependencies.md).
- Skonfiguruj Śledzenie zależności dla [środowiska Java](./java-agent.md).
- [Napisz niestandardową telemetrię zależności](./api-custom-events-metrics.md#trackdependency)
- Zobacz [model danych](data-model.md) dla typów Application Insights i modelu danych.
- Sprawdź [platformy](./platforms.md) obsługiwane przez Application Insights.

