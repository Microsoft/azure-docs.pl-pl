---
title: Model danych telemetrii metryk — Application Insights platformy Azure
description: Application Insights model danych dla telemetrii metryki
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: f8e787b8a94987e7d68e12a0e4e69f4c63e21e19
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87320582"
---
# <a name="metric-telemetry-application-insights-data-model"></a>Telemetria metryki: Application Insights model danych

Istnieją dwa typy danych telemetrycznych metryk obsługiwane przez [Application Insights](./app-insights-overview.md): pojedyncze pomiary i wstępnie zagregowane metryki. Pojedyncza miara jest tylko nazwą i wartością. Metryka wstępnie zagregowana określa minimalną i maksymalną wartość metryki w interwale agregacji i odchylenie standardowe.

Przed zagregowaną telemetrię Metryka zakłada, że okres agregacji wynosił jedną minutę.

Istnieje kilka dobrze znanych nazw metryk obsługiwanych przez Application Insights. Te metryki znajdują się w tabeli liczniki wydajności.

Metryka reprezentująca liczniki systemowe i procesów:

| **Nazwa platformy .NET**             | **Nazwa niezależny od platformy** | **Nazwa interfejsu API REST** | **Opis**
| ------------------------- | -------------------------- | ----------------- | ---------------- 
| `\Processor(_Total)\% Processor Time` | Pracuj w toku... | [processorCpuPercentage](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessorCpuPercentage) | Łączny czas procesora komputera
| `\Memory\Available Bytes`                 | Pracuj w toku... | [memoryAvailableBytes](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FmemoryAvailableBytes) | Pokazuje ilość pamięci fizycznej (w bajtach), która jest dostępna dla procesów uruchomionych na komputerze. Jest on obliczany przez zsumowanie ilości miejsca na listach o wartości zero, wolnych i pamięci w stanie gotowości. Wolna pamięć jest gotowa do użycia; wartość zerowa pamięci składa się ze stron pamięci wypełnionej zerem, aby zapobiec wyświetlaniu przez późniejsze procesy danych używanych przez poprzedni proces; pamięć w stanie wstrzymania jest pamięcią, która została usunięta z zestawu roboczego procesu (jego pamięci fizycznej) w trasie do dysku, ale nadal jest dostępna do wywołania. Zobacz [Obiekt pamięci](/previous-versions/ms804008(v=msdn.10))
| `\Process(??APP_WIN32_PROC??)\% Processor Time` | Pracuj w toku... | [processCpuPercentage](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessCpuPercentage) | Procesor CPU procesu obsługującego aplikację
| `\Process(??APP_WIN32_PROC??)\Private Bytes`      | Pracuj w toku... | [processPrivateBytes](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessPrivateBytes) | pamięć używana przez proces obsługujący aplikację
| `\Process(??APP_WIN32_PROC??)\IO Data Bytes/sec` | Pracuj w toku... | [processIOBytesPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FprocessIOBytesPerSecond) | Liczba operacji we/wy wykonywanych przez proces obsługujący aplikację
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Requests/Sec`             | Pracuj w toku... | [requestsPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestsPerSecond) | częstotliwość żądań przetwarzanych przez aplikację 
| `\.NET CLR Exceptions(??APP_CLR_PROC??)\# of Exceps Thrown / sec`    | Pracuj w toku... | [exceptionsPerSecond](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FexceptionsPerSecond) | częstotliwość wyjątków zgłoszonych przez aplikację
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Request Execution Time`   | Pracuj w toku... | [requestExecutionTime](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestExecutionTime) | Średni czas wykonywania żądań
| `\ASP.NET Applications(??APP_W3SVC_PROC??)\Requests In Application Queue` | Pracuj w toku... | [requestsInQueue](https://dev.applicationinsights.io/apiexplorer/metrics?appId=DEMO_APP&apiKey=DEMO_KEY&metricId=performanceCounters%2FrequestsInQueue) | Liczba żądań oczekujących na przetworzenie w kolejce

## <a name="name"></a>Nazwa

Nazwa metryki, którą chcesz zobaczyć w portalu Application Insights i interfejsie użytkownika. 

## <a name="value"></a>Wartość

Pojedyncza wartość miary. Suma pojedynczych pomiarów dla agregacji.

## <a name="count"></a>Liczba

Waga metryki zagregowanej metryki. Nie należy ustawiać dla miary.

## <a name="min"></a>Min.

Minimalna wartość zagregowanej metryki. Nie należy ustawiać dla miary.

## <a name="max"></a>Maks.

Maksymalna wartość zagregowanej metryki. Nie należy ustawiać dla miary.

## <a name="standard-deviation"></a>Odchylenie standardowe

Odchylenie standardowe zagregowanej metryki. Nie należy ustawiać dla miary.

## <a name="custom-properties"></a>Właściwości niestandardowe

Metryka z ustawioną właściwością niestandardową `CustomPerfCounter` `true` wskazującą, że Metryka reprezentuje licznik wydajności systemu Windows. Te metryki zostały umieszczone w tabeli liczniki wydajności. Nie w customMetrics. Nazwa tej metryki jest również analizowana w celu wyodrębnienia nazwy kategorii, licznika i wystąpienia.

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak używać [interfejsu API Application Insights dla niestandardowych zdarzeń i metryk](./api-custom-events-metrics.md#trackmetric).
- Zobacz [model danych](data-model.md) dla typów Application Insights i modelu danych.
- Sprawdź [platformy](./platforms.md) obsługiwane przez Application Insights.

