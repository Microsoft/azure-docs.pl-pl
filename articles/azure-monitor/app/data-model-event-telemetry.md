---
title: Azure telemetria usługi Application Insights Data Model — Telemetria zdarzeń | Microsoft Docs
description: Application Insights model danych telemetrii zdarzeń
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 69685afa14352a22b58bccbea342038e4273696e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87320616"
---
# <a name="event-telemetry-application-insights-data-model"></a>Telemetrię zdarzeń: Application Insights model danych

Można utworzyć elementy telemetrii zdarzeń (w [Application Insights](./app-insights-overview.md)) do reprezentowania zdarzenia, które wystąpiło w aplikacji. Zwykle jest to interakcja użytkownika, taka jak kliknięcie przycisku lub zamówienie wyewidencjonowania. Może to być również zdarzenie dotyczące cyklu życia aplikacji, takie jak Inicjalizacja lub aktualizacja konfiguracji. 

Semantycznie zdarzenia mogą lub nie mogą być skorelowane z żądaniami. Jednak jeśli są używane prawidłowo, dane telemetryczne zdarzeń są ważniejsze niż żądania lub ślady. Zdarzenia reprezentują dane telemetryczne i powinny być podmiotem do oddzielenia, mniej agresywnych [próbek](./api-filtering-sampling.md).

## <a name="name"></a>Nazwa

Nazwa zdarzenia. Aby umożliwić prawidłowe grupowanie i przydatne metryki, Ogranicz aplikację, tak aby generowała małą liczbę oddzielnych nazw zdarzeń. Na przykład nie należy używać oddzielnej nazwy dla każdego wygenerowanego wystąpienia zdarzenia.

Maksymalna długość: 512 znaków

## <a name="custom-properties"></a>Właściwości niestandardowe

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Pomiary niestandardowe

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Następne kroki

- Zobacz [model danych](data-model.md) dla typów Application Insights i modelu danych.
- [Napisz niestandardową telemetrię zdarzeń](./api-custom-events-metrics.md#trackevent)
- Sprawdź [platformy](./platforms.md) obsługiwane przez Application Insights.

