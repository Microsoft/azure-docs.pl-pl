---
title: Odwołanie do interfejsu API agenta usługi Azure Application Insights
description: Odwołanie do interfejsu API agenta usługi Application Insights. Get-ApplicationInsightsMonitoringConfig. Monitoruj działanie witryny bez ponownego rozmieszczania witryny. Współpracuje ze ASP.NET aplikacjami sieci web hostowanymi lokalnie, na maszynach wirtualnych lub na platformie Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 0cb5991b381acc9a0ec36113d0e15a7731b5074d
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537495"
---
# <a name="application-insights-agent-api-get-applicationinsightsmonitoringconfig"></a>Interfejs API agenta usługi Application Insights: Get-ApplicationInsightsMonitoringConfig

W tym artykule opisano polecenie cmdlet, który jest członkiem [modułu Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Opis

Pobiera plik konfiguracyjny i drukuje wartości do konsoli.

> [!IMPORTANT] 
> To polecenie cmdlet wymaga sesji programu PowerShell z uprawnieniami administratora.

## <a name="examples"></a>Przykłady

```powershell
PS C:\> Get-ApplicationInsightsMonitoringConfig
```

## <a name="parameters"></a>Parametry

Nie są wymagane żadne parametry.

## <a name="output"></a>Dane wyjściowe


#### <a name="example-output-from-reading-the-config-file"></a>Przykładowy wynik z odczytu pliku konfiguracyjnego

```
RedfieldConfiguration:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
```

## <a name="next-steps"></a>Następne kroki

  Wyświetlanie telemetrii:
 - [Eksploruj metryki,](../../azure-monitor/platform/metrics-charts.md) aby monitorować wydajność i użycie.
- [Szukaj zdarzeń i dzienników](../../azure-monitor/app/diagnostic-search.md) w celu zdiagnozowania problemów.
- Użyj [analizy](../../azure-monitor/app/analytics.md) dla bardziej zaawansowanych zapytań.
- [Tworzenie pulpitów nawigacyjnych](../../azure-monitor/app/overview-dashboard.md).
 
 Dodawanie kolejnych funkcji telemetrii:
 - [Tworzenie testów sieci Web](monitor-web-app-availability.md), aby upewnić się, że witryna pozostaje aktywna.
- [Dodaj dane telemetryczne klienta sieci Web,](../../azure-monitor/app/javascript.md) aby wyświetlić wyjątki od kodu strony sieci web i włączyć śledzenie wywołań.
- [Dodaj SDK usługi Application Insights do kodu, dzięki](../../azure-monitor/app/asp-net.md) czemu można wstawić śledzenie i rejestrowanie wywołań.
 
 Więcej informacji za pomocą agenta usługi Application Insights:
 - Skorzystaj z naszego [przewodnika,](status-monitor-v2-troubleshoot.md) aby rozwiązać problem z agentem aplikacji Insights.
 - Wprowadzanie zmian w konfiguracji za pomocą polecenia cmdlet [Set config.](status-monitor-v2-api-set-config.md)
