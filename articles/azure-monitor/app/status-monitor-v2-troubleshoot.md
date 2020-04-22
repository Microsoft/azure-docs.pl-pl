---
title: Rozwiązywanie problemów z usługą Azure Application Insights Agent i znane problemy | Dokumenty firmy Microsoft
description: Znane problemy agenta usługi Application Insights i przykłady rozwiązywania problemów. Monitoruj działanie witryny bez ponownego rozmieszczania witryny. Współpracuje ze ASP.NET aplikacjami sieci web hostowanymi lokalnie, na maszynach wirtualnych lub na platformie Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 9bb22b12a7b3e972ff144bd121db4288801e2488
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732950"
---
# <a name="troubleshooting-application-insights-agent-formerly-named-status-monitor-v2"></a>Rozwiązywanie problemów z agentem usługi Application Insights Agent (dawniej o nazwie Monitor stanu w wersji 2)

Po włączeniu monitorowania mogą wystąpić problemy, które uniemożliwiają zbieranie danych.
W tym artykule wymieniono wszystkie znane problemy i przedstawiono przykłady rozwiązywania problemów.
Jeśli natkniesz się na problem, którego nie ma tutaj, możesz skontaktować się z nami na [GitHub](https://github.com/Microsoft/ApplicationInsights-Home/issues).

## <a name="known-issues"></a>Znane problemy

### <a name="conflicting-dlls-in-an-apps-bin-directory"></a>Sprzeczne biblioteki DLL w katalogu pojemników aplikacji

Jeśli którakolwiek z tych bibliotek DLL znajduje się w katalogu pojemników, monitorowanie może zakończyć się niepowodzeniem:

- Plik DLL aplikacji firmy Microsoft.Application
- Plik Dll Microsoft.AspNet.TelemetryCorrelation.dll
- Plik System.Diagnostics.DiagnosticSource.dll

Niektóre z tych bibliotek DLL są zawarte w domyślnych szablonach aplikacji programu Visual Studio, nawet jeśli aplikacja ich nie używa.
Za pomocą narzędzi do rozwiązywania problemów można zobaczyć zachowanie objawowe:

- Perfview:
    ```
    ThreadID="7,500" 
    ProcessorNumber="0" 
    msg="Found 'System.Diagnostics.DiagnosticSource, Version=4.0.2.1, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51' assembly, skipping attaching redfield binaries" 
    ExtVer="2.8.13.5972" 
    SubscriptionId="" 
    AppName="" 
    FormattedMessage="Found 'System.Diagnostics.DiagnosticSource, Version=4.0.2.1, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51' assembly, skipping attaching redfield binaries" 
    ```

- IISReset i obciążenie aplikacji (bez danych telemetrycznych). Zbadaj za pomocą sysinternals (Handle.exe i ListDLLs.exe):
    ```
    .\handle64.exe -p w3wp | findstr /I "InstrumentationEngine AI. ApplicationInsights"
    E54: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll

    .\Listdlls64.exe w3wp | findstr /I "InstrumentationEngine AI ApplicationInsights"
    0x0000000009be0000  0x127000  C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll
    0x0000000009b90000  0x4f000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll
    0x0000000004d20000  0xb2000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.Extensions.Base_x64.dll
    ```

### <a name="conflict-with-iis-shared-configuration"></a>Konflikt z konfiguracją udostępnioną usług IIS

Jeśli masz klaster serwerów sieci web, być może używasz [konfiguracji udostępnionej](https://docs.microsoft.com/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211).
Nie można wstrzyknąć modułu http do tej konfiguracji udostępnionej.
Uruchom polecenie Włącz na każdym serwerze sieci web, aby zainstalować bibliotekę DLL w pliku GAC każdego serwera.

Po uruchomieniu polecenia Włącz wykonaj następujące czynności:
1. Przejdź do udostępnionego katalogu konfiguracji i znajdź plik applicationHost.config.
2. Dodaj ten wiersz do sekcji modułów konfiguracji:
    ```
    <modules>
        <!-- Registered global managed http module handler. The 'Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll' must be installed in the GAC before this config is applied. -->
        <add name="ManagedHttpModuleHelper" type="Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.ManagedHttpModuleHelper, Microsoft.AppInsights.IIS.ManagedHttpModuleHelper, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" preCondition="managedHandler,runtimeVersionv4.0" />
    </modules>
    ```

### <a name="iis-nested-applications"></a>Aplikacje zagnieżdżone w aplikacjach IIS

Nie instruujemy zagnieżdżonych aplikacji w uzywaczach IIS w wersji 1.0.
Śledzimy ten problem [tutaj](https://github.com/microsoft/ApplicationInsights-Home/issues/369).

### <a name="advanced-sdk-configuration-isnt-available"></a>Zaawansowana konfiguracja SDK nie jest dostępna.

Konfiguracja SDK nie jest narażona na użytkownika końcowego w wersji 1.0.
Śledzimy ten problem [tutaj](https://github.com/microsoft/ApplicationInsights-Home/issues/375).

    
    
## <a name="troubleshooting"></a>Rozwiązywanie problemów
    
### <a name="troubleshooting-powershell"></a>Rozwiązywanie problemów z programem PowerShell

#### <a name="determine-which-modules-are-available"></a>Określanie, które moduły są dostępne
Za pomocą `Get-Module -ListAvailable` polecenia można określić, które moduły są zainstalowane.

#### <a name="import-a-module-into-the-current-session"></a>Importowanie modułu do bieżącej sesji
Jeśli moduł nie został załadowany do sesji programu PowerShell, można `Import-Module <path to psd1>` ręcznie załadować go za pomocą polecenia.


### <a name="troubleshooting-the-application-insights-agent-module"></a>Rozwiązywanie problemów z modułem Agent usługi Application Insights

#### <a name="list-the-commands-available-in-the-application-insights-agent-module"></a>Wyświetlanie listy poleceń dostępnych w module Agent usługi Application Insights
Uruchom polecenie, `Get-Command -Module Az.ApplicationMonitor` aby uzyskać dostępne polecenia:

```
CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Disable-ApplicationInsightsMonitoring              0.4.0      Az.ApplicationMonitor
Cmdlet          Disable-InstrumentationEngine                      0.4.0      Az.ApplicationMonitor
Cmdlet          Enable-ApplicationInsightsMonitoring               0.4.0      Az.ApplicationMonitor
Cmdlet          Enable-InstrumentationEngine                       0.4.0      Az.ApplicationMonitor
Cmdlet          Get-ApplicationInsightsMonitoringConfig            0.4.0      Az.ApplicationMonitor
Cmdlet          Get-ApplicationInsightsMonitoringStatus            0.4.0      Az.ApplicationMonitor
Cmdlet          Set-ApplicationInsightsMonitoringConfig            0.4.0      Az.ApplicationMonitor
Cmdlet          Start-ApplicationInsightsMonitoringTrace           0.4.0      Az.ApplicationMonitor
```

#### <a name="determine-the-current-version-of-the-application-insights-agent-module"></a>Określanie bieżącej wersji modułu Agent usługi Application Insights
Uruchom `Get-ApplicationInsightsMonitoringStatus -PowerShellModule` polecenie, aby wyświetlić następujące informacje o module:
   - Wersja modułu Programu PowerShell
   - Wersja SDK usługi Application Insights
   - Ścieżki plików modułu programu PowerShell
    
Przejrzyj [odwołanie do interfejsu API,](status-monitor-v2-api-reference.md) aby uzyskać szczegółowy opis sposobu używania tego polecenia cmdlet.


### <a name="troubleshooting-running-processes"></a>Rozwiązywanie problemów z uruchomionymi procesami

Można sprawdzić procesy na komputerze oprzyrządowanym, aby ustalić, czy wszystkie biblioteki DLL są ładowane.
Jeśli monitorowanie działa, należy załadować co najmniej 12 bibliotek DLL.

Użyj `Get-ApplicationInsightsMonitoringStatus -InspectProcess` polecenia, aby sprawdzić biblioteki DLL.

Przejrzyj [odwołanie do interfejsu API,](status-monitor-v2-api-reference.md) aby uzyskać szczegółowy opis sposobu używania tego polecenia cmdlet.


### <a name="collect-etw-logs-by-using-perfview"></a>Zbieranie dzienników ETW przy użyciu PerfView

#### <a name="setup"></a>Konfigurowanie

1. Pobierz pliki PerfView.exe i PerfView64.exe z [gitHub](https://github.com/Microsoft/perfview/releases).
2. Uruchom plik PerfView64.exe.
3. Rozwiń **opcje zaawansowane**.
4. Wyczyść te pola wyboru:
    - **Zip**
    - **Merge**
    - **Kolekcja symboli .NET**
5. Ustaw tych **dodatkowych dostawców:**`61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,925fa42b-9ef6-5fa7-10b8-56449d7a2040,f7d60e07-e910-5aca-bdd2-9de45b46c560,7c739bb9-7861-412e-ba50-bf30d95eae36,61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,252e28f4-43f9-5771-197a-e8c7e750a984`


#### <a name="collecting-logs"></a>Zbieranie dzienników

1. W konsoli poleceń z uprawnieniami `iisreset /stop` administratora uruchom polecenie, aby wyłączyć usługi IIS i wszystkie aplikacje internetowe.
2. W obszarze PerfView wybierz pozycję **Rozpocznij zbieranie danych**.
3. W konsoli poleceń z uprawnieniami `iisreset /start` administratora uruchom polecenie, aby uruchomić usługi IIS.
4. Spróbuj przejść do aplikacji.
5. Po załadowaniu aplikacji wróć do PerfView i wybierz **pozycję Zatrzymaj kolekcję**.



## <a name="next-steps"></a>Następne kroki

- Przejrzyj [odwołanie do interfejsu API,](status-monitor-v2-overview.md#powershell-api-reference) aby dowiedzieć się więcej o parametrach, które mogły zostać pominięte.
- Jeśli natkniesz się na problem, którego nie ma tutaj, możesz skontaktować się z nami na [GitHub](https://github.com/Microsoft/ApplicationInsights-Home/issues).
