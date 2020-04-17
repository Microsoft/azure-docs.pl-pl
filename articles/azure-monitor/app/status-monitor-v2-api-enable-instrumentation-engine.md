---
title: Odwołanie do interfejsu API agenta usługi Azure Application Insights
description: Odwołanie do interfejsu API agenta usługi Application Insights. Włącz-InstrumentacjaInżyj. Monitoruj działanie witryny bez ponownego rozmieszczania witryny. Współpracuje ze ASP.NET aplikacjami sieci web hostowanymi lokalnie, na maszynach wirtualnych lub na platformie Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 70102395b94c89d4ce0edfddeda1df0be0e2f216
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536764"
---
# <a name="application-insights-agent-api-enable-instrumentationengine"></a>Interfejs API agenta usługi Application Insights: Enable-InstrumentationEngine

W tym artykule opisano polecenie cmdlet, który jest członkiem [modułu Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Opis

Włącza aparat instrumentacji, ustawiając niektóre klucze rejestru.
Uruchom ponownie usługę IIS, aby zmiany zostały wprowadzone.

Aparat instrumentacji może uzupełniać dane zebrane przez zestawy SDK .NET.
Zbiera zdarzenia i komunikaty, które opisują wykonanie procesu zarządzanego. Te zdarzenia i komunikaty obejmują kody wyników zależności, zlecenia HTTP i [tekst polecenia SQL](asp-net-dependencies.md#advanced-sql-tracking-to-get-full-sql-query).

Włączyć silnik oprzyrządowania, jeśli:
- Monitorowanie jest już włączone za pomocą polecenia cmdlet Enable, ale nie włączyłeś aparatu instrumentacji.
- Aplikacja została ręcznie zaarządowana za pomocą zestawów SDK platformy .NET i ma być zbierana dodatkowa telemetria.

> [!IMPORTANT] 
> To polecenie cmdlet wymaga sesji programu PowerShell z uprawnieniami administratora.

> [!NOTE] 
> - To polecenie cmdlet wymaga zapoznania się i zaakceptowania naszej licencji i oświadczenia o ochronie prywatności.
> - Aparat oprzyrządowania dodaje dodatkowe obciążenie i jest domyślnie wyłączony.

## <a name="examples"></a>Przykłady

```powershell
PS C:\> Enable-InstrumentationEngine
```

## <a name="parameters"></a>Parametry

### <a name="-acceptlicense"></a>-AcceptLicencja
**Opcjonalne.** Ten przełącznik służy do akceptowania licencji i zasad zachowania poufności informacji w instalacjach bezgłowych.

### <a name="-verbose"></a>-Pełne
**Wspólny parametr.** Użyj tego przełącznika do wyprowadzania szczegółowych dzienników.

## <a name="output"></a>Dane wyjściowe


#### <a name="example-output-from-successfully-enabling-the-instrumentation-engine"></a>Przykładowy wynik z pomyślnego włączenia aparatu oprzyrządowania

```
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
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
 - [Pobierz config,](status-monitor-v2-api-get-config.md) aby potwierdzić, że ustawienia zostały nagrane poprawnie.
 - [Uzyskaj stan,](status-monitor-v2-api-get-status.md) aby sprawdzić monitorowanie.
