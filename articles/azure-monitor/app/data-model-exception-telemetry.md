---
title: Model danych telemetrii wyjątku usługi Azure Application Insights
description: Application Insights model danych dla telemetrii wyjątku
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 6732fb3dde79abd5c69e2b0a91710bdd2356d6aa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87320599"
---
# <a name="exception-telemetry-application-insights-data-model"></a>Dane telemetryczne wyjątku: Application Insights model danych

W [Application Insights](./app-insights-overview.md)wystąpienie wyjątku reprezentuje obsłużony lub nieobsługiwany wyjątek, który wystąpił podczas wykonywania monitorowanej aplikacji.

## <a name="problem-id"></a>Identyfikator problemu

Identyfikator, pod którym został zgłoszony wyjątek w kodzie. Używany do grupowania wyjątków. Zwykle kombinacja typu wyjątku i funkcji ze stosu wywołań.

Maksymalna długość: 1024 znaków

## <a name="severity-level"></a>Poziom ważności

Poziom ważności śledzenia. Wartość może być `Verbose` , `Information` ,,, `Warning` `Error` `Critical` .

## <a name="exception-details"></a>Szczegóły wyjątku

(Do rozszerzenia)

## <a name="custom-properties"></a>Właściwości niestandardowe

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Pomiary niestandardowe

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Następne kroki

- Zobacz [model danych](data-model.md) dla typów Application Insights i modelu danych.
- Dowiedz się, jak [zdiagnozować wyjątki w aplikacjach sieci Web za pomocą Application Insights](./asp-net-exceptions.md).
- Sprawdź [platformy](./platforms.md) obsługiwane przez Application Insights.

