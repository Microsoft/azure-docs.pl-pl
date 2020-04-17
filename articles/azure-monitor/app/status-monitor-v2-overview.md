---
title: Usługa Azure Application Insights — omówienie | Dokumenty firmy Microsoft
description: Omówienie agenta usługi Application Insights. Monitoruj działanie witryny bez ponownego rozmieszczania witryny. Współpracuje ze ASP.NET aplikacjami sieci web hostowanymi lokalnie, na maszynach wirtualnych lub na platformie Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 09/16/2019
ms.openlocfilehash: 4042fc05f278915fe72bf8fc4e6afd69a5bbb4b9
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537427"
---
# <a name="deploy-azure-monitor-application-insights-agent-for-on-premises-servers"></a>Wdrażanie agenta usługi Azure Monitor Application Insights dla serwerów lokalnych

> [!IMPORTANT]
> Te wskazówki są zalecane dla lokalnych i innych niż Azure wdrożeń usługi Cloud agenta usługi Application Insights. Oto zalecane podejście do [wdrożeń platformy Azure dla maszyn wirtualnych i zestawów skalowania maszyny wirtualnej.](https://docs.microsoft.com/azure/azure-monitor/app/azure-vm-vmss-apps)

Application Insights Agent (dawniej Status Monitor V2) to moduł programu PowerShell opublikowany w [Galerii programu PowerShell.](https://www.powershellgallery.com/packages/Az.ApplicationMonitor)
Zastępuje [Monitor stanu](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now).
Telemetria jest wysyłana do witryny Azure portal, gdzie można [monitorować](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) aplikację.

> [!NOTE]
> Moduł obsługuje tylko obecnie instrumentację bezkodowych aplikacji internetowych platformy .NET hostowanych przy systemach IIS. Użyj zestawu SDK do instruowania ASP.NET aplikacji Core, Java i Node.js.

## <a name="powershell-gallery"></a>Galeria programu PowerShell

Usługa Application Insights Agent https://www.powershellgallery.com/packages/Az.ApplicationMonitorznajduje się tutaj: .

![Galeria programu PowerShell](https://img.shields.io/powershellgallery/v/Az.ApplicationMonitor.svg?color=Blue&label=Current%20Version&logo=PowerShell&style=for-the-badge)


## <a name="instructions"></a>Instrukcje
- Zapoznaj się z [instrukcjami dotyczącymi rozpoczynania](status-monitor-v2-get-started.md) pracy, aby rozpocząć pracę z zwięzłymi przykładami kodu.
- Zobacz [szczegółowe instrukcje](status-monitor-v2-detailed-instructions.md) dotyczące głębokiego nurkowania, jak rozpocząć pracę.

## <a name="powershell-api-reference"></a>Odwołanie do interfejsu API programu PowerShell
- [Monitorowanie disable-ApplicationInsights](status-monitor-v2-api-disable-monitoring.md)
- [Inwalidacja InstrumentacjaEngyna](status-monitor-v2-api-disable-instrumentation-engine.md)
- [Monitorowanie wysuwające aplikacje enable](status-monitor-v2-api-enable-monitoring.md)
- [Włącz-InstrumentacjaInżynie](status-monitor-v2-api-enable-instrumentation-engine.md)
- [Get-ApplicationInsightsMonitoringConfig](status-monitor-v2-api-get-config.md)
- [Stan monitorowania aplikacji Get-Application](status-monitor-v2-api-get-status.md)
- [Set-ApplicationInsightsMonitoringConfig](status-monitor-v2-api-set-config.md)
- [Śledzenie monitorowania aplikacji startowych](status-monitor-v2-api-start-trace.md)

## <a name="troubleshooting"></a>Rozwiązywanie problemów
- [Rozwiązywanie problemów](status-monitor-v2-troubleshoot.md)
- [Znane problemy](status-monitor-v2-troubleshoot.md#known-issues)


## <a name="faq"></a>Często zadawane pytania

- Czy agent usługi Application Insights obsługuje instalacje proxy?

  *Tak*. Istnieje wiele sposobów pobierania agenta usługi Application Insights Agent. Jeśli komputer ma dostęp do Internetu, można onboard do `-Proxy` Galerii programu PowerShell przy użyciu parametrów.
Można również ręcznie pobrać moduł i zainstalować go na komputerze lub użyć go bezpośrednio.
Każda z tych opcji jest opisana w [szczegółowych instrukcjach](status-monitor-v2-detailed-instructions.md).

- Czy Monitor stanu w wersji 2 obsługuje aplikacje ASP.NET Core?

  *Nie*. Aby uzyskać instrukcje umożliwiające monitorowanie ASP.NET aplikacji Core, zobacz [Usługa Application Insights dla aplikacji ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core). Nie ma potrzeby instalowania StatusMonitor dla aplikacji ASP.NET Core. Jest to prawdą, nawet jeśli ASP.NET podstawowa aplikacja jest hostowana w usługach IIS.

- Jak sprawdzić, czy włączenie powiodło się?

  - Polecenie cmdlet [Get-ApplicationInsightsMonitoringStatus](status-monitor-v2-api-get-status.md) może służyć do sprawdzenia, czy włączenie powiodło się.
  - Zalecamy użycie [metryk na żywo,](https://docs.microsoft.com/azure/azure-monitor/app/live-stream) aby szybko określić, czy aplikacja wysyła dane telemetryczne.

  - Za pomocą [usługi Log Analytics](../log-query/get-started-portal.md) można również wyświetlić listę wszystkich ról w chmurze aktualnie wysyłających dane telemetryczne:
      ```Kusto
      union * | summarize count() by cloud_RoleName, cloud_RoleInstance
      ```

## <a name="next-steps"></a>Następne kroki

Wyświetlanie telemetrii:

* [Eksploruj metryki,](../../azure-monitor/platform/metrics-charts.md) aby monitorować wydajność i użycie.
* [Szukaj zdarzeń i dzienników](../../azure-monitor/app/diagnostic-search.md) w celu zdiagnozowania problemów.
* [Korzystaj z analizy](../../azure-monitor/app/analytics.md) w przypadku bardziej zaawansowanych zapytań.
* [Tworzenie pulpitów nawigacyjnych](../../azure-monitor/app/overview-dashboard.md).

Dodawanie kolejnych funkcji telemetrii:

* [Tworzenie testów sieci Web](monitor-web-app-availability.md), aby upewnić się, że witryna pozostaje aktywna.
* [Dodaj dane telemetryczne klienta sieci Web,](../../azure-monitor/app/javascript.md) aby wyświetlić wyjątki od kodu strony sieci web i włączyć śledzenie wywołań.
* [Dodaj SDK usługi Application Insights do kodu, dzięki](../../azure-monitor/app/asp-net.md) czemu można wstawić śledzenie i rejestrowanie wywołań.

