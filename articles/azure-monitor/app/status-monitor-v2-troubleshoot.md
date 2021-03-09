---
title: Rozwiązywanie problemów z usługą Azure Application Insights Agent i znane problemy | Microsoft Docs
description: Znane problemy dotyczące Application Insights agenta i rozwiązywania problemów. Monitorowanie wydajności witryny sieci Web bez ponownego wdrażania witryny sieci Web. Współpracuje z usługą ASP.NET Web Apps hostowaną lokalnie, na maszynach wirtualnych lub na platformie Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: be202a6016842ee82b3230c2841d416d3b53ea15
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102505583"
---
# <a name="troubleshooting-application-insights-agent-formerly-named-status-monitor-v2"></a>Rozwiązywanie problemów z agentem Application Insights (dawniej nazwany monitor stanu v2)

Po włączeniu monitorowania mogą wystąpić problemy uniemożliwiające zbieranie danych.
W tym artykule wymieniono wszystkie znane problemy i przedstawiono przykłady rozwiązywania problemów.

## <a name="known-issues"></a>Znane problemy

### <a name="conflicting-dlls-in-an-apps-bin-directory"></a>Konflikty bibliotek DLL w katalogu bin aplikacji

Jeśli dowolna z tych bibliotek dll znajduje się w katalogu bin, monitorowanie może zakończyć się niepowodzeniem:

- Microsoft.ApplicationInsights.dll
- Microsoft.AspNet.TelemetryCorrelation.dll
- System.Diagnostics.DiagnosticSource.dll

Niektóre z tych bibliotek DLL są zawarte w domyślnych szablonach aplikacji programu Visual Studio, nawet jeśli aplikacja nie korzysta z nich.
Możesz użyć narzędzi do rozwiązywania problemów, aby zobaczyć zachowanie objawem:

- Narzędzia PerfView
    ```
    ThreadID="7,500" 
    ProcessorNumber="0" 
    msg="Found 'System.Diagnostics.DiagnosticSource, Version=4.0.2.1, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51' assembly, skipping attaching redfield binaries" 
    ExtVer="2.8.13.5972" 
    SubscriptionId="" 
    AppName="" 
    FormattedMessage="Found 'System.Diagnostics.DiagnosticSource, Version=4.0.2.1, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51' assembly, skipping attaching redfield binaries" 
    ```

- IISReset i obciążenie aplikacji (bez telemetrii). Zbadaj przy użyciu programu Sysinternals (Handle.exe i ListDLLs.exe):
    ```
    .\handle64.exe -p w3wp | findstr /I "InstrumentationEngine AI. ApplicationInsights"
    E54: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll

    .\Listdlls64.exe w3wp | findstr /I "InstrumentationEngine AI ApplicationInsights"
    0x0000000009be0000  0x127000  C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll
    0x0000000009b90000  0x4f000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll
    0x0000000004d20000  0xb2000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.Extensions.Base_x64.dll
    ```

### <a name="powershell-versions"></a>Wersje programu PowerShell
Ten produkt został zapisany i przetestowany przy użyciu programu PowerShell v 5.1.
Ten moduł nie jest zgodny z programem PowerShell w wersji 6 lub 7.
Zalecamy korzystanie z programu PowerShell w wersji 5.1 wraz z nowszymi wersjami. Aby uzyskać więcej informacji, zobacz [Korzystanie z programu PowerShell 7 obok programu powershell 5,1](/powershell/scripting/install/migrating-from-windows-powershell-51-to-powershell-7#using-powershell-7-side-by-side-with-windows-powershell-51).

### <a name="conflict-with-iis-shared-configuration"></a>Konflikt z udostępnioną konfiguracją usług IIS

Jeśli masz klaster serwerów sieci Web, być może używasz [konfiguracji udostępnionej](/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211).
Nie można wstrzyknąć modułu HttpModule do tej konfiguracji udostępnionej.
Uruchom polecenie Enable na każdym serwerze sieci Web, aby zainstalować bibliotekę DLL w poszczególnych serwerach GAC.

Po uruchomieniu polecenia Enable wykonaj następujące czynności:
1. Przejdź do katalogu konfiguracji udostępnionej i Znajdź plik applicationHost.config.
2. Dodaj ten wiersz do sekcji modułów w konfiguracji:
    ```
    <modules>
        <!-- Registered global managed http module handler. The 'Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll' must be installed in the GAC before this config is applied. -->
        <add name="ManagedHttpModuleHelper" type="Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.ManagedHttpModuleHelper, Microsoft.AppInsights.IIS.ManagedHttpModuleHelper, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" preCondition="managedHandler,runtimeVersionv4.0" />
    </modules>
    ```

### <a name="iis-nested-applications"></a>Aplikacje zagnieżdżone usług IIS

Nie instrumentuje zagnieżdżonych aplikacji w usługach IIS w wersji 1,0.

### <a name="advanced-sdk-configuration-isnt-available"></a>Zaawansowana konfiguracja zestawu SDK jest niedostępna.

Konfiguracja zestawu SDK nie jest dostępna dla użytkownika końcowego w wersji 1,0.

    
    
## <a name="troubleshooting"></a>Rozwiązywanie problemów
    
### <a name="troubleshooting-powershell"></a>Rozwiązywanie problemów z programem PowerShell

#### <a name="determine-which-modules-are-available"></a>Określanie, które moduły są dostępne
Możesz użyć polecenia, `Get-Module -ListAvailable` Aby określić, które moduły są zainstalowane.

#### <a name="import-a-module-into-the-current-session"></a>Importowanie modułu do bieżącej sesji
Jeśli moduł nie został załadowany do sesji programu PowerShell, można go załadować ręcznie przy użyciu `Import-Module <path to psd1>` polecenia.


### <a name="troubleshooting-the-application-insights-agent-module"></a>Rozwiązywanie problemów z modułem agenta Application Insights

#### <a name="list-the-commands-available-in-the-application-insights-agent-module"></a>Wyświetl listę poleceń dostępnych w module agenta Application Insights
Uruchom polecenie, `Get-Command -Module Az.ApplicationMonitor` Aby pobrać dostępne polecenia:

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

#### <a name="determine-the-current-version-of-the-application-insights-agent-module"></a>Określ bieżącą wersję modułu Application Insights Agent
Uruchom `Get-ApplicationInsightsMonitoringStatus -PowerShellModule` polecenie, aby wyświetlić następujące informacje dotyczące modułu:
   - Wersja modułu programu PowerShell
   - Wersja zestawu SDK Application Insights
   - Ścieżki plików modułu programu PowerShell
    
Zapoznaj się z dokumentacją [interfejsu API](status-monitor-v2-api-reference.md) w celu uzyskania szczegółowego opisu sposobu korzystania z tego polecenia cmdlet.


### <a name="troubleshooting-running-processes"></a>Rozwiązywanie problemów z uruchamianiem procesów

Można sprawdzić procesy na komputerze z instrumentacją, aby ustalić, czy wszystkie biblioteki DLL zostały załadowane.
Jeśli monitorowanie działa, należy załadować co najmniej 12 bibliotek DLL.

Użyj `Get-ApplicationInsightsMonitoringStatus -InspectProcess` polecenia, aby sprawdzić biblioteki DLL.

Zapoznaj się z dokumentacją [interfejsu API](status-monitor-v2-api-reference.md) w celu uzyskania szczegółowego opisu sposobu korzystania z tego polecenia cmdlet.


### <a name="collect-etw-logs-by-using-perfview"></a>Zbieranie dzienników ETW przy użyciu narzędzia PerfView

#### <a name="setup"></a>Konfigurowanie

1. Pobierz PerfView.exe i PerfView64.exe z usługi [GitHub](https://github.com/Microsoft/perfview/releases).
2. Rozpocznij PerfView64.exe.
3. Rozwiń **Opcje zaawansowane**.
4. Wyczyść następujące pola wyboru:
    - **Kodu**
    - **Merge**
    - **Kolekcja symboli platformy .NET**
5. Ustaw tych **dodatkowych dostawców**: `61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,925fa42b-9ef6-5fa7-10b8-56449d7a2040,f7d60e07-e910-5aca-bdd2-9de45b46c560,7c739bb9-7861-412e-ba50-bf30d95eae36,61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,252e28f4-43f9-5771-197a-e8c7e750a984`


#### <a name="collecting-logs"></a>Zbieranie dzienników

1. W konsoli poleceń z uprawnieniami administratora uruchom `iisreset /stop` polecenie, aby wyłączyć usługi IIS i wszystkie aplikacje sieci Web.
2. W narzędzia PerfView wybierz pozycję **Rozpocznij zbieranie**.
3. W konsoli poleceń z uprawnieniami administratora uruchom `iisreset /start` polecenie, aby uruchomić usługi IIS.
4. Spróbuj przejść do swojej aplikacji.
5. Po załadowaniu aplikacji Wróć do narzędzia PerfView i wybierz pozycję **Zatrzymaj zbieranie**.



## <a name="next-steps"></a>Następne kroki

- Przejrzyj [odwołanie do interfejsu API](status-monitor-v2-overview.md#powershell-api-reference) , aby dowiedzieć się więcej na temat parametrów, które mogły zostać pominięte.