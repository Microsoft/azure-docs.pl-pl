---
title: Azure telemetria usługi Application Insights Data Model — Telemetria zdarzeń | Microsoft Docs
description: Application Insights model danych telemetrii zdarzeń
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: bd8b2581f7642f6825aaf0d1b51c8e94d4333d33
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "77671887"
---
# <a name="event-telemetry-application-insights-data-model"></a>Telemetrię zdarzeń: Application Insights model danych

Można utworzyć elementy telemetrii zdarzeń (w [Application Insights](../../azure-monitor/app/app-insights-overview.md)) do reprezentowania zdarzenia, które wystąpiło w aplikacji. Zwykle jest to interakcja użytkownika, taka jak kliknięcie przycisku lub zamówienie wyewidencjonowania. Może to być również zdarzenie dotyczące cyklu życia aplikacji, takie jak Inicjalizacja lub aktualizacja konfiguracji. 

Semantycznie zdarzenia mogą lub nie mogą być skorelowane z żądaniami. Jednak jeśli są używane prawidłowo, dane telemetryczne zdarzeń są ważniejsze niż żądania lub ślady. Zdarzenia reprezentują dane telemetryczne i powinny być podmiotem do oddzielenia, mniej agresywnych [próbek](../../azure-monitor/app/api-filtering-sampling.md).

## <a name="name"></a>Nazwa

Nazwa zdarzenia. Aby umożliwić prawidłowe grupowanie i przydatne metryki, Ogranicz aplikację, tak aby generowała małą liczbę oddzielnych nazw zdarzeń. Na przykład nie należy używać oddzielnej nazwy dla każdego wygenerowanego wystąpienia zdarzenia.

Maksymalna długość: 512 znaków

## <a name="custom-properties"></a>Właściwości niestandardowe

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Pomiary niestandardowe

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Następne kroki

- Zobacz [model danych](data-model.md) dla typów Application Insights i modelu danych.
- [Napisz niestandardową telemetrię zdarzeń](../../azure-monitor/app/api-custom-events-metrics.md#trackevent)
- Sprawdź [platformy](../../azure-monitor/app/platforms.md) obsługiwane przez Application Insights.
