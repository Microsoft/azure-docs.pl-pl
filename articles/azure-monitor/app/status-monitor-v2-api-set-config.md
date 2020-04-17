---
title: Odwołanie do interfejsu API agenta usługi Azure Application Insights
description: Odwołanie do interfejsu API agenta usługi Application Insights. Set-ApplicationInsightsMonitoringConfig. Monitoruj działanie witryny bez ponownego rozmieszczania witryny. Współpracuje ze ASP.NET aplikacjami sieci web hostowanymi lokalnie, na maszynach wirtualnych lub na platformie Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: c47b9b5f297fa62c474e6c29737d6d11b887130d
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537478"
---
# <a name="application-insights-agent-api-set-applicationinsightsmonitoringconfig"></a>Interfejs API agenta usługi Application Insights: Set-ApplicationInsightsMonitoringConfig

W tym dokumencie opisano polecenie cmdlet, które jest członkiem [modułu Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Opis

Ustawia plik konfiguracyjny bez pełnej ponownej instalacji.
Uruchom ponownie usługę IIS, aby zmiany zostały wprowadzone.

> [!IMPORTANT] 
> To polecenie cmdlet wymaga sesji programu PowerShell z uprawnieniami administratora.


## <a name="examples"></a>Przykłady

### <a name="example-with-a-single-instrumentation-key"></a>Przykład z jednym kluczem oprzyrządowania
W tym przykładzie wszystkim aplikacjom na bieżącym komputerze zostanie przypisany jeden klucz instrumentacji.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

### <a name="example-with-an-instrumentation-key-map"></a>Przykład z mapą klucza oprzyrządowania
W tym przykładzie:
- `MachineFilter`dopasowuje bieżący `'.*'` komputer przy użyciu symbolu wieloznacznego.
- `AppFilter='WebAppExclude'`zapewnia `null` klucz oprzyrządowania. Określona aplikacja nie będzie instrumentowana.
- `AppFilter='WebAppOne'`przypisuje określonej aplikacji unikatowy klucz instrumentacji.
- `AppFilter='WebAppTwo'`przypisuje określonej aplikacji unikatowy klucz instrumentacji.
- Na koniec `AppFilter` używa również `'.*'` symbol wieloznaczny, aby dopasować wszystkie aplikacje sieci web, które nie są dopasowane przez wcześniejsze reguły i przypisać domyślny klucz instrumentacji.
- Spacje są dodawane w celu czytelności.

```powershell
Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap `
       @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
          @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'}},
          @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'}},
          @{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'}})
```

## <a name="parameters"></a>Parametry

### <a name="-instrumentationkey"></a>-InstrumentationKey
**Wymagany.** Ten parametr służy do poniesienia pojedynczego klucza instrumentacji do użycia przez wszystkie aplikacje na komputerze docelowym.

### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**Wymagany.** Ten parametr służy do dostarczania wielu kluczy instrumentacji i mapowania kluczy instrumentacji używanych przez każdą aplikację.
Można utworzyć pojedynczy skrypt instalacyjny `MachineFilter`dla kilku komputerów, ustawiając plik .

> [!IMPORTANT]
> Aplikacje będą zgodne z regułami w kolejności, w. Dlatego należy najpierw określić najbardziej szczegółowe reguły i ostatnio najbardziej ogólne reguły.

#### <a name="schema"></a>Schemat
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'})`

- **MachineFilter** jest wymaganym wyrażeniem języka C# nazwy komputera lub maszyny Wirtualnej.
    - '.*' będzie pasować do wszystkich
    - "Nazwa komputera" będzie zgodna tylko z komputerami o określonej nazwie.
- **AppFilter** jest wymaganym wyrażeniem języka C# nazwy komputera lub maszyny Wirtualnej.
    - '.*' będzie pasować do wszystkich
    - "ApplicationName" będzie pasować tylko do aplikacji IIS o określonej nazwie.
- **InstrumentationKey** jest wymagane, aby włączyć monitorowanie aplikacji, które pasują do poprzednich dwóch filtrów.
    - Pozostaw tę wartość null, jeśli chcesz zdefiniować reguły, aby wykluczyć monitorowanie.


### <a name="-verbose"></a>-Pełne
**Wspólny parametr.** Ten przełącznik służy do wyświetlania szczegółowych dzienników.


## <a name="output"></a>Dane wyjściowe

Domyślnie nie ma danych wyjściowych.

#### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkey"></a>Przykład pełne dane wyjściowe z ustawienia pliku konfiguracyjnego za pośrednictwem -InstrumentationKey

```
VERBOSE: Operation: InstallWithIkey
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

#### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkeymap"></a>Przykład pełne dane wyjściowe z ustawienia pliku konfiguracyjnego za pośrednictwem -InstrumentationKeyMap

```
VERBOSE: Operation: InstallWithIkeyMap
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

## <a name="next-steps"></a>Następne kroki

  Wyświetlanie telemetrii:
 - [Eksploruj metryki,](../../azure-monitor/platform/metrics-charts.md) aby monitorować wydajność i użycie.
- [Szukaj zdarzeń i dzienników](../../azure-monitor/app/diagnostic-search.md) w celu zdiagnozowania problemów.
- [Korzystaj z analizy](../../azure-monitor/app/analytics.md) w przypadku bardziej zaawansowanych zapytań.
- [Tworzenie pulpitów nawigacyjnych](../../azure-monitor/app/overview-dashboard.md).
 
 Dodawanie kolejnych funkcji telemetrii:
 - [Tworzenie testów sieci Web](monitor-web-app-availability.md), aby upewnić się, że witryna pozostaje aktywna.
- [Dodaj dane telemetryczne klienta sieci Web,](../../azure-monitor/app/javascript.md) aby wyświetlić wyjątki od kodu strony sieci web i włączyć śledzenie wywołań.
- [Dodaj sdk usługi Application Insights do kodu, aby](../../azure-monitor/app/asp-net.md) można było wstawiać wywołania śledzenia i rejestrowania
 
 Więcej informacji za pomocą agenta usługi Application Insights:
 - Skorzystaj z naszego [przewodnika,](status-monitor-v2-troubleshoot.md) aby rozwiązać problem z agentem aplikacji Insights.
 - [Pobierz config,](status-monitor-v2-api-get-config.md) aby potwierdzić, że ustawienia zostały nagrane poprawnie.
 - [Uzyskaj stan,](status-monitor-v2-api-get-status.md) aby sprawdzić monitorowanie.
