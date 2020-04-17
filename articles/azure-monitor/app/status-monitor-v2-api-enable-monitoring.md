---
title: Odwołanie do interfejsu API agenta usługi Azure Application Insights
description: Odwołanie do interfejsu API agenta usługi Application Insights. Enable-ApplicationInsightsMonitoring. Monitoruj działanie witryny bez ponownego rozmieszczania witryny. Współpracuje ze ASP.NET aplikacjami sieci web hostowanymi lokalnie, na maszynach wirtualnych lub na platformie Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: f5d66c5c21f7491e3bdc6bb70f693c3b98bf62dd
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536747"
---
# <a name="application-insights-agent-api-enable-applicationinsightsmonitoring"></a>Interfejs API agenta usługi Application Insights: Enable-ApplicationInsightsMonitoring

W tym artykule opisano polecenie cmdlet, który jest członkiem [modułu Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Opis

Umożliwia bezkodowe dołączanie monitorowania aplikacji usług IIS na komputerze docelowym.

To polecenie cmdlet zmodyfikuje aplikację IISHost.config i ustawi niektóre klucze rejestru.
Utworzy również plik applicationinsights.ikey.config, który definiuje klucz instrumentacji używany przez każdą aplikację.
Usługi IIS załadują RedfieldModule podczas uruchamiania, co spowoduje wstrzyknąć sdk usługi Application Insights do aplikacji podczas uruchamiania aplikacji.
Uruchom ponownie usługę IIS, aby zmiany zostały wprowadzone.

Po włączeniu monitorowania zaleca się użycie [metryk na żywo,](live-stream.md) aby szybko sprawdzić, czy aplikacja wysyła nam dane telemetryczne.


> [!NOTE] 
> - Aby rozpocząć, potrzebny jest klucz oprzyrządowania. Aby uzyskać więcej informacji, zobacz [Tworzenie zasobu](create-new-resource.md#copy-the-instrumentation-key).
> - To polecenie cmdlet wymaga zapoznania się i zaakceptowania naszej licencji i oświadczenia o ochronie prywatności.

> [!IMPORTANT] 
> To polecenie cmdlet wymaga sesji programu PowerShell z uprawnieniami administratora i zasadami wykonywania z podwyższonym poziomem uprawnień. Aby uzyskać więcej informacji, zobacz [Uruchamianie programu PowerShell jako administratora z zasadami wykonywania z podwyższonym poziomem uprawnień](status-monitor-v2-detailed-instructions.md#run-powershell-as-admin-with-an-elevated-execution-policy).

## <a name="examples"></a>Przykłady

### <a name="example-with-a-single-instrumentation-key"></a>Przykład z jednym kluczem oprzyrządowania
W tym przykładzie wszystkie aplikacje na bieżącym komputerze są przypisane do jednego klucza instrumentacji.

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
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap 
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
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'}})`

- **MachineFilter** jest wymaganym wyrażeniem języka C# nazwy komputera lub maszyny Wirtualnej.
    - '.*' będzie pasować do wszystkich
    - "ComputerName" będzie pasować tylko do komputerów o dokładnej nazwie.
- **AppFilter** jest wymaganym wyrażeniem języka C# nazwy witryny usługi IIS. Listę witryn na serwerze można uzyskać, uruchamiając polecenie [get-iissite](https://docs.microsoft.com/powershell/module/iisadministration/get-iissite).
    - '.*' będzie pasować do wszystkich
    - "SiteName" będzie pasować tylko do witryny IIS z dokładną określoną nazwą.
- **InstrumentationKey** jest wymagane, aby włączyć monitorowanie aplikacji, które pasują do poprzednich dwóch filtrów.
    - Pozostaw tę wartość null, jeśli chcesz zdefiniować reguły, aby wykluczyć monitorowanie.


### <a name="-enableinstrumentationengine"></a>-EnableInstrumentationEngine
**Opcjonalne.** Użyj tego przełącznika, aby włączyć aparat instrumentacji do zbierania zdarzeń i komunikatów o tym, co dzieje się podczas wykonywania procesu zarządzanego. Te zdarzenia i komunikaty obejmują kody wyników zależności, zlecenia HTTP i tekst polecenia SQL.

Aparat oprzyrządowania dodaje obciążenie i jest domyślnie wyłączony.

### <a name="-acceptlicense"></a>-AcceptLicencja
**Opcjonalne.** Ten przełącznik służy do akceptowania licencji i zasad zachowania poufności informacji w instalacjach bezgłowych.

### <a name="-ignoresharedconfig"></a>-IgnoreSharedConfig
Jeśli masz klaster serwerów sieci web, być może używasz [konfiguracji udostępnionej](https://docs.microsoft.com/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211).
Nie można wstrzyknąć modułu http do tej konfiguracji udostępnionej.
Ten skrypt zakończy się niepowodzeniem z komunikatem, że wymagane są dodatkowe kroki instalacji.
Użyj tego przełącznika, aby zignorować tę kontrolę i kontynuować instalowanie wymagań wstępnych. Aby uzyskać więcej informacji, zobacz [znane konflikty z konfiguracją współużytkową](status-monitor-v2-troubleshoot.md#conflict-with-iis-shared-configuration)

### <a name="-verbose"></a>-Pełne
**Wspólny parametr.** Ten przełącznik służy do wyświetlania szczegółowych dzienników.

### <a name="-whatif"></a>-CoIf 
**Wspólny parametr.** Ten przełącznik służy do testowania i sprawdzania poprawności parametrów wejściowych bez włączania monitorowania.

## <a name="output"></a>Dane wyjściowe


#### <a name="example-output-from-a-successful-enablement"></a>Przykładowe dane wyjściowe z pomyślnego włączenia

```
Initiating Disable Process
Applying transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config'
'C:\Windows\System32\inetsrv\config\applicationHost.config' backed up to 'C:\Windows\System32\inetsrv\config\applicationHost.config.backup-2019-03-26_08-59-52z'
in :1,237
No element in the source document matches '/configuration/location[@path='']/system.webServer/modules/add[@name='ManagedHttpModuleHelper']'
Not executing RemoveAll (transform line 1, 546)
Transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config' was successfully applied. Operation: 'disable'
GAC Module will not be removed, since this operation might cause IIS instabilities
Configuring IIS Environment for codeless attach...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring IIS Environment for instrumentation engine...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring registry for instrumentation engine...
Successfully disabled Application Insights Status Monitor
Installing GAC module 'C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.0\content\Runtime\Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll'
Applying transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config'
Found GAC module Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.ManagedHttpModuleHelper, Microsoft.AppInsights.IIS.ManagedHttpModuleHelper, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35
'C:\Windows\System32\inetsrv\config\applicationHost.config' backed up to 'C:\Windows\System32\inetsrv\config\applicationHost.config.backup-2019-03-26_08-59-52z_1'
Transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config' was successfully applied. Operation: 'enable'
Configuring IIS Environment for codeless attach...
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
Updating app pool permissions...
Successfully enabled Application Insights Status Monitor
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
- [Dodaj SDK usługi Application Insights do kodu, dzięki](../../azure-monitor/app/asp-net.md) czemu można wstawić śledzenie i rejestrowanie wywołań.
 
 Więcej informacji za pomocą agenta usługi Application Insights:
 - Skorzystaj z naszego [przewodnika,](status-monitor-v2-troubleshoot.md) aby rozwiązać problem z agentem aplikacji Insights.
 - [Pobierz config,](status-monitor-v2-api-get-config.md) aby potwierdzić, że ustawienia zostały nagrane poprawnie.
 - [Uzyskaj stan,](status-monitor-v2-api-get-status.md) aby sprawdzić monitorowanie.
