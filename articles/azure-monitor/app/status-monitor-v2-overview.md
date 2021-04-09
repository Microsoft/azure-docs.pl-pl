---
title: Omówienie usługi Azure Application Insights Agent | Microsoft Docs
description: Przegląd Application Insights agenta. Monitorowanie wydajności witryny sieci Web bez ponownego wdrażania witryny sieci Web. Współpracuje z usługą ASP.NET Web Apps hostowaną lokalnie, na maszynach wirtualnych lub na platformie Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 09/16/2019
ms.openlocfilehash: 69f80856150e461c6edfafdf0aa89de77c4ab0fd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100583814"
---
# <a name="deploy-azure-monitor-application-insights-agent-for-on-premises-servers"></a>Wdróż agenta Azure Monitor Application Insights dla serwerów lokalnych

> [!IMPORTANT]
> Te wskazówki są zalecane w przypadku wdrożeń Application Insights agenta lokalnych i nieopartych na platformie Azure w chmurze. Poniżej przedstawiono zalecane podejście do [wdrożeń maszyn wirtualnych platformy Azure i zestawu skalowania maszyn wirtualnych](./azure-vm-vmss-apps.md).

Agent Application Insights (wcześniej nazwany monitor stanu v2) to moduł programu PowerShell opublikowany w [Galeria programu PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor).
Zastępuje [Monitor stanu](./monitor-performance-live-website-now.md).
Dane telemetryczne są wysyłane do Azure Portal, w którym można [monitorować](./app-insights-overview.md) aplikację.

> [!NOTE]
> Moduł obsługuje obecnie tylko instrumentację programu .NET Web Apps hostowaną z usługami IIS. Użyj zestawu SDK do Instrumentacji aplikacji ASP.NET Core, Java i Node.js.

## <a name="powershell-gallery"></a>Galeria programu PowerShell

Agent Application Insights znajduje się tutaj: https://www.powershellgallery.com/packages/Az.ApplicationMonitor .

![Galeria programu PowerShell](https://img.shields.io/powershellgallery/v/Az.ApplicationMonitor.svg?color=Blue&label=Current%20Version&logo=PowerShell&style=for-the-badge)


## <a name="instructions"></a>Instrukcje
- Zobacz [instrukcje wprowadzające](status-monitor-v2-get-started.md) , aby rozpocząć od zwięzłych przykładów kodu.
- Zobacz [szczegółowe instrukcje](status-monitor-v2-detailed-instructions.md) dotyczące głębokiej szczegółowe na temat rozpoczynania pracy.

## <a name="powershell-api-reference"></a>Dokumentacja interfejsu API programu PowerShell
- [Disable-ApplicationInsightsMonitoring](./status-monitor-v2-api-reference.md#disable-applicationinsightsmonitoring)
- [Disable-InstrumentationEngine](./status-monitor-v2-api-reference.md#disable-instrumentationengine)
- [Enable-ApplicationInsightsMonitoring](./status-monitor-v2-api-reference.md#enable-applicationinsightsmonitoring)
- [Enable-InstrumentationEngine](./status-monitor-v2-api-reference.md#enable-instrumentationengine)
- [Get-ApplicationInsightsMonitoringConfig](./status-monitor-v2-api-reference.md#get-applicationinsightsmonitoringconfig)
- [Get-ApplicationInsightsMonitoringStatus](./status-monitor-v2-api-reference.md#get-applicationinsightsmonitoringstatus)
- [Set-ApplicationInsightsMonitoringConfig](./status-monitor-v2-api-reference.md#set-applicationinsightsmonitoringconfig)
- [Start-ApplicationInsightsMonitoringTrace](./status-monitor-v2-api-reference.md#start-applicationinsightsmonitoringtrace)

## <a name="troubleshooting"></a>Rozwiązywanie problemów
- [Rozwiązywanie problemów](status-monitor-v2-troubleshoot.md)
- [Znane problemy](status-monitor-v2-troubleshoot.md#known-issues)


## <a name="faq"></a>Często zadawane pytania

- Czy Agent Application Insights obsługuje instalacje serwera proxy?

  *Tak*. Istnieje wiele sposobów pobierania Application Insights agenta. Jeśli komputer ma dostęp do Internetu, możesz dołączyć do Galeria programu PowerShell za pomocą `-Proxy` parametrów.
Możesz również ręcznie pobrać moduł, a następnie zainstalować go na komputerze lub użyć go bezpośrednio.
Każda z tych opcji została opisana w [szczegółowym instrukcji](status-monitor-v2-detailed-instructions.md).

- Czy monitor stanu v2 obsługuje ASP.NET Core aplikacje?

  *Nie*. Aby uzyskać instrukcje dotyczące włączania monitorowania aplikacji ASP.NET Core, zobacz [Application Insights for ASP.NET Core Applications](./asp-net-core.md). Nie trzeba instalować StatusMonitor dla aplikacji ASP.NET Core. Jest to prawdziwe, nawet jeśli aplikacja ASP.NET Core jest hostowana w usługach IIS.

- Jak mogę sprawdzić, czy Włączanie zakończyło się pomyślnie?

  - Polecenia cmdlet [Get-ApplicationInsightsMonitoringStatus](./status-monitor-v2-api-reference.md#get-applicationinsightsmonitoringstatus) można użyć do sprawdzenia, czy Włączanie powiodło się.
  - Zalecamy używanie metryk na [żywo](./live-stream.md) , aby szybko określić, czy aplikacja wysyła dane telemetryczne.

  - Możesz również użyć [log Analytics](../logs/log-analytics-tutorial.md) , aby wyświetlić listę wszystkich ról w chmurze aktualnie wysyłających dane telemetryczne:
      ```Kusto
      union * | summarize count() by cloud_RoleName, cloud_RoleInstance
      ```

## <a name="next-steps"></a>Następne kroki

Wyświetlanie telemetrii:

* [Poznaj metryki](../essentials/metrics-charts.md) , aby monitorować wydajność i użycie.
* [Wyszukaj zdarzenia i dzienniki](./diagnostic-search.md) , aby zdiagnozować problemy.
* [Użyj analizy](../logs/log-query-overview.md) , aby uzyskać bardziej zaawansowane zapytania.
* [Tworzenie pulpitów nawigacyjnych](./overview-dashboard.md).

Dodawanie kolejnych funkcji telemetrii:

* [Tworzenie testów sieci Web](monitor-web-app-availability.md), aby upewnić się, że witryna pozostaje aktywna.
* [Dodaj telemetrię klienta sieci Web](./javascript.md) , aby zobaczyć wyjątki z kodu strony sieci Web i włączyć wywołania śledzenia.
* [Dodaj do kodu zestaw SDK Application Insights](./asp-net.md) , aby móc wstawiać wywołania śledzenia i rejestrowania.