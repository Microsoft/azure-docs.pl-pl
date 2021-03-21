---
title: Dokumentacja interfejsu API usługi Azure Application Insights .NET Agent
description: Dokumentacja interfejsu API agenta Application Insights. Monitorowanie wydajności witryny sieci Web bez ponownego wdrażania witryny sieci Web. Współpracuje z usługą ASP.NET Web Apps hostowaną lokalnie, na maszynach wirtualnych lub na platformie Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 2278b9d70e888fa546dc64da4743b2bf5b6c45e8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100587527"
---
# <a name="azure-monitor-application-insights-agent-api-reference"></a>Dokumentacja interfejsu API agenta Application Insights Azure Monitor

W tym artykule opisano polecenie cmdlet, które jest członkiem [modułu programu PowerShell AZ. ApplicationMonitor](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!NOTE] 
> - Aby rozpocząć, musisz dysponować kluczem Instrumentacji. Aby uzyskać więcej informacji, zobacz [Tworzenie zasobu](create-new-resource.md#copy-the-instrumentation-key).
> - To polecenie cmdlet wymaga przejrzenia i zaakceptowania naszych licencji i zasad zachowania poufności informacji.

> [!IMPORTANT] 
> To polecenie cmdlet wymaga sesji programu PowerShell z uprawnieniami administratora i zasadami wykonywania podwyższonego poziomu uprawnień. Aby uzyskać więcej informacji, zobacz [Uruchamianie programu PowerShell jako administrator z zasadami wykonywania podwyższonego poziomu uprawnień](status-monitor-v2-detailed-instructions.md#run-powershell-as-admin-with-an-elevated-execution-policy).
> - To polecenie cmdlet wymaga przejrzenia i zaakceptowania naszych licencji i zasad zachowania poufności informacji.
> - Aparat Instrumentacji dodaje dodatkowe obciążenie i jest domyślnie wyłączony.


## <a name="enable-instrumentationengine"></a>Enable-InstrumentationEngine

Włącza aparat Instrumentacji przez ustawienie niektórych kluczy rejestru.
Uruchom ponownie usługi IIS, aby zmiany zaczęły obowiązywać.

Aparat Instrumentacji może uzupełniać dane zbierane przez zestawy SDK platformy .NET.
Zbiera zdarzenia i komunikaty opisujące wykonywanie procesu zarządzanego. Te zdarzenia i komunikaty obejmują kody wyników zależności, zlecenia HTTP i [tekst polecenia SQL](asp-net-dependencies.md#advanced-sql-tracking-to-get-full-sql-query).

Włącz aparat instrumentacji, jeśli:
- Monitorowanie zostało już włączone za pomocą polecenia cmdlet Enable, ale nie włączono aparatu Instrumentacji.
- Twoja aplikacja została ręcznie przypięta przy użyciu zestawów SDK platformy .NET i chcesz zebrać dodatkową telemetrię.

### <a name="examples"></a>Przykłady

```powershell
PS C:\> Enable-InstrumentationEngine
```

### <a name="parameters"></a>Parametry

#### <a name="-acceptlicense"></a>-AcceptLicense
**Obowiązkowe.** Ten przełącznik umożliwia zaakceptowanie licencji i zasad zachowania poufności informacji w instalacjach bezobsługowych.

#### <a name="-verbose"></a>-Verbose
**Wspólny parametr.** Ten przełącznik umożliwia wyprowadzanie szczegółowych dzienników.

### <a name="output"></a>Dane wyjściowe


##### <a name="example-output-from-successfully-enabling-the-instrumentation-engine"></a>Przykładowe dane wyjściowe pomyślnie włączania aparatu Instrumentacji

```
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
```

## <a name="enable-applicationinsightsmonitoring"></a>Enable-ApplicationInsightsMonitoring

Włącza bezkodowe monitorowanie aplikacji usług IIS na komputerze docelowym.

To polecenie cmdlet zmodyfikuje applicationHost.config IIS i ustawi niektóre klucze rejestru.
Utworzy również plik applicationinsights.ikey.config, który definiuje klucz Instrumentacji używany przez poszczególne aplikacje.
Usługi IIS będą ładować RedfieldModule przy uruchamianiu, co spowoduje wstrzyknięcie zestawu SDK Application Insights do aplikacji w miarę uruchamiania aplikacji.
Uruchom ponownie usługi IIS, aby zmiany zaczęły obowiązywać.

Po włączeniu monitorowania zalecamy korzystanie z [metryk na żywo](live-stream.md) , aby szybko sprawdzić, czy aplikacja wysyła do nas dane telemetryczne.

### <a name="examples"></a>Przykłady

#### <a name="example-with-a-single-instrumentation-key"></a>Przykład z pojedynczym kluczem Instrumentacji
W tym przykładzie wszystkie aplikacje na bieżącym komputerze mają przypisany pojedynczy klucz Instrumentacji.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

#### <a name="example-with-an-instrumentation-key-map"></a>Przykład z mapą kluczy Instrumentacji
W tym przykładzie:
- `MachineFilter` dopasowuje bieżący komputer przy użyciu `'.*'` symbolu wieloznacznego.
- `AppFilter='WebAppExclude'` udostępnia `null` klucz Instrumentacji. Określona aplikacja nie będzie Instrumentacją.
- `AppFilter='WebAppOne'` przypisuje określoną aplikację unikatowy klucz Instrumentacji.
- `AppFilter='WebAppTwo'` przypisuje określoną aplikację unikatowy klucz Instrumentacji.
- Na koniec `AppFilter` używa również `'.*'` symboli wieloznacznych, aby dopasować wszystkie aplikacje sieci Web, które nie są zgodne ze starszymi regułami i przypisać domyślny klucz Instrumentacji.
- Spacje są dodawane do czytelności.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap 
    @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
      @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'}},
      @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'}},
      @{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'}})

```


### <a name="parameters"></a>Parametry

#### <a name="-instrumentationkey"></a>-InstrumentationKey
**Wymagane.** Użyj tego parametru, aby dostarczyć pojedynczy klucz instrumentacji do użycia przez wszystkie aplikacje na komputerze docelowym.

#### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**Wymagane.** Użyj tego parametru, aby podać wiele kluczy Instrumentacji i mapowanie kluczy Instrumentacji używanych przez poszczególne aplikacje.
Można utworzyć pojedynczy skrypt instalacyjny dla kilku komputerów przez ustawienie `MachineFilter` .

> [!IMPORTANT]
> Aplikacje będą zgodne z regułami w kolejności, w jakiej są udostępniane reguły. Dlatego należy najpierw określić najbardziej szczegółowe reguły i najbardziej ogólne reguły.

##### <a name="schema"></a>Schemat
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'}})`

- **MachineFilter** to wymagane wyrażenie regularne języka C# nazwy komputera lub maszyny wirtualnej.
    - ". *" będzie pasować do wszystkich
    - Nazwa "ComputerName" będzie pasować tylko do komputerów o określonej nazwie.
- **AppFilter** to wymagane wyrażenie regularne języka C# nazwy witryny usług IIS. Listę witryn na serwerze można uzyskać, uruchamiając polecenie [Get-iissite](/powershell/module/iisadministration/get-iissite).
    - ". *" będzie pasować do wszystkich
    - Wartość "sitename" będzie pasować tylko do witryny usług IIS z określoną dokładną nazwą.
- **InstrumentationKey** jest wymagana, aby umożliwić monitorowanie aplikacji, które pasują do poprzednich dwóch filtrów.
    - Pozostaw tę wartość null, jeśli chcesz zdefiniować reguły do wykluczenia monitorowania.


#### <a name="-enableinstrumentationengine"></a>-EnableInstrumentationEngine
**Obowiązkowe.** Użyj tego przełącznika, aby umożliwić aparatowi Instrumentacji zbieranie zdarzeń i komunikatów na temat tego, co się dzieje w trakcie wykonywania procesu zarządzanego. Te zdarzenia i komunikaty obejmują kody wyników zależności, zlecenia HTTP i tekst polecenia SQL.

Aparat Instrumentacji dodaje obciążenie i jest domyślnie wyłączony.

#### <a name="-acceptlicense"></a>-AcceptLicense
**Obowiązkowe.** Ten przełącznik umożliwia zaakceptowanie licencji i zasad zachowania poufności informacji w instalacjach bezobsługowych.

#### <a name="-ignoresharedconfig"></a>-IgnoreSharedConfig
W przypadku klastra serwerów sieci Web może być używana [Konfiguracja udostępniona](/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211).
Nie można wstrzyknąć modułu HttpModule do tej konfiguracji udostępnionej.
Ten skrypt zakończy się niepowodzeniem z komunikatem, że wymagane jest wykonanie dodatkowych czynności instalacyjnych.
Użyj tego przełącznika, aby zignorować to sprawdzenie i kontynuować instalowanie wymagań wstępnych. Aby uzyskać więcej informacji, zobacz [znane konflikty z-with-IIS — konfiguracja udostępniona](status-monitor-v2-troubleshoot.md#conflict-with-iis-shared-configuration)

#### <a name="-verbose"></a>-Verbose
**Wspólny parametr.** Użyj tego przełącznika, aby wyświetlić szczegółowe dzienniki.

#### <a name="-whatif"></a>-WhatIf 
**Wspólny parametr.** Za pomocą tego przełącznika można testować i weryfikować parametry wejściowe bez faktycznego włączenia monitorowania.

### <a name="output"></a>Dane wyjściowe

#### <a name="example-output-from-a-successful-enablement"></a>Przykładowe dane wyjściowe z pomyślnego włączenia

```powershell
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

## <a name="disable-instrumentationengine"></a>Disable-InstrumentationEngine

Wyłącza aparat instrumentacji, usuwając niektóre klucze rejestru.
Uruchom ponownie usługi IIS, aby zmiany zaczęły obowiązywać.

### <a name="examples"></a>Przykłady

```powershell
PS C:\> Disable-InstrumentationEngine
```

### <a name="parameters"></a>Parametry 

#### <a name="-verbose"></a>-Verbose
**Wspólny parametr.** Ten przełącznik umożliwia wyprowadzanie szczegółowych dzienników.

### <a name="output"></a>Dane wyjściowe


##### <a name="example-output-from-successfully-disabling-the-instrumentation-engine"></a>Przykładowe dane wyjściowe pomyślnie wyłączające aparat Instrumentacji

```powershell
Configuring IIS Environment for instrumentation engine...
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]'
Configuring registry for instrumentation engine...
```

## <a name="disable-applicationinsightsmonitoring"></a>Disable-ApplicationInsightsMonitoring

Wyłącza monitorowanie na komputerze docelowym.
To polecenie cmdlet spowoduje usunięcie zmian applicationHost.config usług IIS i usunięcie kluczy rejestru.

### <a name="examples"></a>Przykłady

```powershell
PS C:\> Disable-ApplicationInsightsMonitoring
```

### <a name="parameters"></a>Parametry 

#### <a name="-verbose"></a>-Verbose
**Wspólny parametr.** Użyj tego przełącznika, aby wyświetlić szczegółowe dzienniki.

### <a name="output"></a>Dane wyjściowe


##### <a name="example-output-from-successfully-disabling-monitoring"></a>Przykładowe dane wyjściowe z pomyślnym wyłączeniem monitorowania

```powershell
Initiating Disable Process
Applying transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config'
'C:\Windows\System32\inetsrv\config\applicationHost.config' backed up to 'C:\Windows\System32\inetsrv\config\applicationHost.config.backup-2019-03-26_08-59-00z'
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
```


## <a name="get-applicationinsightsmonitoringconfig"></a>Get-ApplicationInsightsMonitoringConfig

Pobiera plik konfiguracji i drukuje wartości w konsoli programu.

### <a name="examples"></a>Przykłady

```powershell
PS C:\> Get-ApplicationInsightsMonitoringConfig
```

### <a name="parameters"></a>Parametry

Nie są wymagane żadne parametry.

### <a name="output"></a>Dane wyjściowe


##### <a name="example-output-from-reading-the-config-file"></a>Przykładowe dane wyjściowe odczytywania pliku konfiguracji

```
RedfieldConfiguration:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
```

## <a name="get-applicationinsightsmonitoringstatus"></a>Get-ApplicationInsightsMonitoringStatus

To polecenie cmdlet zawiera informacje dotyczące rozwiązywania problemów dotyczących monitor stanu.
Użyj tego polecenia cmdlet, aby sprawdzić stan monitorowania, wersję modułu programu PowerShell i sprawdzić uruchomiony proces.
To polecenie cmdlet będzie zgłaszać informacje o wersji i informacje o plikach kluczy wymaganych do monitorowania.

### <a name="examples"></a>Przykłady

#### <a name="example-application-status"></a>Przykład: stan aplikacji

Uruchom polecenie, `Get-ApplicationInsightsMonitoringStatus` Aby wyświetlić stan monitorowania witryn sieci Web.

```powershell

PS C:\Windows\system32> Get-ApplicationInsightsMonitoringStatus

IIS Websites:

SiteName               : Default Web Site
ApplicationPoolName    : DefaultAppPool
SiteId                 : 1
SiteState              : Stopped

SiteName               : DemoWebApp111
ApplicationPoolName    : DemoWebApp111
SiteId                 : 2
SiteState              : Started
ProcessId              : not found

SiteName               : DemoWebApp222
ApplicationPoolName    : DemoWebApp222
SiteId                 : 3
SiteState              : Started
ProcessId              : 2024
Instrumented           : true
InstrumentationKey     : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx123

SiteName               : DemoWebApp333
ApplicationPoolName    : DemoWebApp333
SiteId                 : 4
SiteState              : Started
ProcessId              : 5184
AppAlreadyInstrumented : true
```

W tym przykładzie:
- **Identyfikator maszyny** to anonimowy identyfikator używany do jednoznacznego identyfikowania Twojego serwera. Jeśli utworzysz żądanie pomocy technicznej, będziemy potrzebować tego identyfikatora, aby znaleźć dzienniki dla Twojego serwera.
- **Domyślna witryna sieci Web** jest zatrzymana w usługach IIS
- **DemoWebApp111** został uruchomiony w usługach IIS, ale nie otrzymał żadnych żądań. Ten raport pokazuje, że nie ma uruchomionego procesu (identyfikator procesu: nie znaleziono).
- **DemoWebApp222** działa i jest monitorowany (Instrumentacja: true). W oparciu o konfigurację użytkownika klucz Instrumentacji xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx123 został dopasowany do tej witryny.
- **DemoWebApp333** został ręcznie Instrumentacja przy użyciu zestawu SDK Application Insights. Monitor stanu wykrył zestaw SDK i nie będzie monitorował tej lokacji.


#### <a name="example-powershell-module-information"></a>Przykład: informacje o module programu PowerShell

Uruchom polecenie, `Get-ApplicationInsightsMonitoringStatus -PowerShellModule` Aby wyświetlić informacje o bieżącym module:

```powershell

PS C:\> Get-ApplicationInsightsMonitoringStatus -PowerShellModule

PowerShell Module version:
0.4.0-alpha

Application Insights SDK version:
2.9.0.3872

Executing PowerShell Module Assembly:
Microsoft.ApplicationInsights.Redfield.Configurator.PowerShell, Version=2.8.14.11432, Culture=neutral, PublicKeyToken=31bf3856ad364e35

PowerShell Module Directory:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.2\content\PowerShell

Runtime Paths:
ParentDirectory (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content

ConfigurationPath (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config

ManagedHttpModuleHelperPath (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll

RedfieldIISModulePath (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll

InstrumentationEngine86Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation32\MicrosoftInstrumentationEngine_x86.dll

InstrumentationEngine64Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll

InstrumentationEngineExtensionHost86Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation32\Microsoft.ApplicationInsights.ExtensionsHost_x86.dll

InstrumentationEngineExtensionHost64Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll

InstrumentationEngineExtensionConfig86Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation32\Microsoft.InstrumentationEngine.Extensions.config

InstrumentationEngineExtensionConfig64Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.InstrumentationEngine.Extensions.config

ApplicationInsightsSdkPath (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.dll
```

#### <a name="example-runtime-status"></a>Przykład: stan środowiska uruchomieniowego

Możesz sprawdzić proces na komputerze z instrumentacją, aby sprawdzić, czy wszystkie biblioteki DLL zostały załadowane. Jeśli monitorowanie działa, należy załadować co najmniej 12 bibliotek DLL.

Uruchom polecenie `Get-ApplicationInsightsMonitoringStatus -InspectProcess`:


```
PS C:\> Get-ApplicationInsightsMonitoringStatus -InspectProcess

iisreset.exe /status
Status for IIS Admin Service ( IISADMIN ) : Running
Status for Windows Process Activation Service ( WAS ) : Running
Status for Net.Msmq Listener Adapter ( NetMsmqActivator ) : Running
Status for Net.Pipe Listener Adapter ( NetPipeActivator ) : Running
Status for Net.Tcp Listener Adapter ( NetTcpActivator ) : Running
Status for World Wide Web Publishing Service ( W3SVC ) : Running

handle64.exe -accepteula -p w3wp
BF0: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.ServerTelemetryChannel.dll
C58: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.AzureAppServices.dll
C68: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.DependencyCollector.dll
C78: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.WindowsServer.dll
C98: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.Web.dll
CBC: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.PerfCounterCollector.dll
DB0: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.Agent.Intercept.dll
B98: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll
BB4: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.Contracts.dll
BCC: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.Redfield.Lightup.dll
BE0: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.dll

listdlls64.exe -accepteula w3wp
0x0000000019ac0000  0x127000  C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll
0x00000000198b0000  0x4f000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll
0x000000000c460000  0xb2000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.Extensions.Base_x64.dll
0x000000000ad60000  0x108000  C:\Windows\TEMP\2.4.0.0.Microsoft.ApplicationInsights.Extensions.Intercept_x64.dll
```

### <a name="parameters"></a>Parametry

#### <a name="no-parameters"></a>(Brak parametrów)

Domyślnie to polecenie cmdlet będzie zgłaszać stan monitorowania aplikacji sieci Web.
Użyj tej opcji, aby sprawdzić, czy aplikacja została pomyślnie przeprowadzona.
Możesz również sprawdzić, który klucz Instrumentacji został dopasowany do witryny.


#### <a name="-powershellmodule"></a>-PowerShellModule
**Opcjonalne**. Użyj tego przełącznika, aby zgłosić numery wersji i ścieżki bibliotek DLL wymaganych do monitorowania.
Użyj tej opcji, jeśli konieczne jest zidentyfikowanie wersji dowolnej biblioteki DLL, w tym zestawu SDK Application Insights.

#### <a name="-inspectprocess"></a>-InspectProcess

**Opcjonalne**. Użyj tego przełącznika, aby zgłosić, czy usługi IIS są uruchomione.
Zostaną również pobrane zewnętrzne narzędzia, aby określić, czy wymagane biblioteki DLL są ładowane do środowiska uruchomieniowego usług IIS.


Jeśli ten proces nie powiedzie się z jakiegokolwiek powodu, można uruchomić następujące polecenia ręcznie:
- iisreset.exe/status
- [handle64.exe](/sysinternals/downloads/handle) -p w3wp | findstr/I "InstrumentationEngine AI. ApplicationInsights
- [listdlls64.exe](/sysinternals/downloads/listdlls) w3wp | findstr/I "InstrumentationEngine AI ApplicationInsights"


#### <a name="-force"></a>-Force

**Opcjonalne**. Używane tylko z InspectProcess. Użyj tego przełącznika, aby pominąć monit użytkownika, który pojawia się przed pobraniem dodatkowych narzędzi.


## <a name="set-applicationinsightsmonitoringconfig"></a>Set-ApplicationInsightsMonitoringConfig

Ustawia plik konfiguracji bez wykonywania pełnej ponownej instalacji.
Uruchom ponownie usługi IIS, aby zmiany zaczęły obowiązywać.

> [!IMPORTANT] 
> To polecenie cmdlet wymaga sesji programu PowerShell z uprawnieniami administratora.


### <a name="examples"></a>Przykłady

#### <a name="example-with-a-single-instrumentation-key"></a>Przykład z pojedynczym kluczem Instrumentacji
W tym przykładzie do wszystkich aplikacji na bieżącym komputerze zostanie przypisany pojedynczy klucz Instrumentacji.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

#### <a name="example-with-an-instrumentation-key-map"></a>Przykład z mapą kluczy Instrumentacji
W tym przykładzie:
- `MachineFilter` dopasowuje bieżący komputer przy użyciu `'.*'` symbolu wieloznacznego.
- `AppFilter='WebAppExclude'` udostępnia `null` klucz Instrumentacji. Określona aplikacja nie będzie Instrumentacją.
- `AppFilter='WebAppOne'` przypisuje określoną aplikację unikatowy klucz Instrumentacji.
- `AppFilter='WebAppTwo'` przypisuje określoną aplikację unikatowy klucz Instrumentacji.
- Na koniec `AppFilter` używa również `'.*'` symboli wieloznacznych, aby dopasować wszystkie aplikacje sieci Web, które nie są zgodne ze starszymi regułami i przypisać domyślny klucz Instrumentacji.
- Spacje są dodawane do czytelności.

```powershell
Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap `
       @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
          @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'}},
          @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'}},
          @{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'}})
```

### <a name="parameters"></a>Parametry

#### <a name="-instrumentationkey"></a>-InstrumentationKey
**Wymagane.** Użyj tego parametru, aby dostarczyć pojedynczy klucz instrumentacji do użycia przez wszystkie aplikacje na komputerze docelowym.

#### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**Wymagane.** Użyj tego parametru, aby podać wiele kluczy Instrumentacji i mapowanie kluczy Instrumentacji używanych przez poszczególne aplikacje.
Można utworzyć pojedynczy skrypt instalacyjny dla kilku komputerów przez ustawienie `MachineFilter` .

> [!IMPORTANT]
> Aplikacje będą zgodne z regułami w kolejności, w jakiej są udostępniane reguły. Dlatego należy najpierw określić najbardziej szczegółowe reguły i najbardziej ogólne reguły.

##### <a name="schema"></a>Schemat
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'})`

- **MachineFilter** to wymagane wyrażenie regularne języka C# nazwy komputera lub maszyny wirtualnej.
    - ". *" będzie pasować do wszystkich
    - Nazwa "ComputerName" będzie pasować tylko do komputerów o określonej nazwie.
- **AppFilter** to wymagane wyrażenie regularne języka C# nazwy komputera lub maszyny wirtualnej.
    - ". *" będzie pasować do wszystkich
    - "ApplicationName" będzie pasować tylko do aplikacji IIS o określonej nazwie.
- **InstrumentationKey** jest wymagany do włączenia monitorowania aplikacji, które pasują do poprzednich dwóch filtrów.
    - Pozostaw tę wartość null, jeśli chcesz zdefiniować reguły do wykluczenia monitorowania.


#### <a name="-verbose"></a>-Verbose
**Wspólny parametr.** Użyj tego przełącznika, aby wyświetlić szczegółowe dzienniki.


### <a name="output"></a>Dane wyjściowe

Domyślnie Brak danych wyjściowych.

##### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkey"></a>Przykład pełnych danych wyjściowych dotyczących ustawiania pliku konfiguracji za pomocą parametru-InstrumentationKey

```
VERBOSE: Operation: InstallWithIkey
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

##### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkeymap"></a>Przykład pełnych danych wyjściowych dotyczących ustawiania pliku konfiguracji za pomocą parametru-InstrumentationKeyMap

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

## <a name="start-applicationinsightsmonitoringtrace"></a>Start-ApplicationInsightsMonitoringTrace

Zbiera [zdarzenia ETW](/windows/desktop/etw/event-tracing-portal) z środowiska wykonawczego dołączania bezkodowego. To polecenie cmdlet jest alternatywą dla uruchamiania [Narzędzia PerfView](https://github.com/microsoft/perfview).

Zebrane zdarzenia będą drukowane w konsoli programu w czasie rzeczywistym i zapisane w pliku ETL. Wyjściowy plik ETL może być otwarty przez [Narzędzia PerfView](https://github.com/microsoft/perfview) do dalszej analizy.

To polecenie cmdlet zostanie uruchomione do momentu osiągnięcia limitu czasu trwania (domyślnie 5 minut) lub zostanie zatrzymane ręcznie ( `Ctrl + C` ).

### <a name="examples"></a>Przykłady

#### <a name="how-to-collect-events"></a>Jak zbierać zdarzenia

Zwykle będziemy zbierać zdarzenia w celu zbadania, Dlaczego aplikacja nie jest Instrumentacją.

Środowisko uruchomieniowe dołączania bezkodowego będzie emitować zdarzenia ETW podczas uruchamiania usług IIS i uruchamiania aplikacji.

Aby zebrać te zdarzenia:
1. W konsoli cmd z uprawnieniami administratora wykonaj polecenie, `iisreset /stop` Aby wyłączyć usługi IIS i wszystkie aplikacje sieci Web.
2. Wykonaj to polecenie cmdlet
3. W konsoli cmd z uprawnieniami administratora uruchom polecenie, `iisreset /start` Aby uruchomić usługi IIS.
4. Spróbuj przejść do swojej aplikacji.
5. Po zakończeniu ładowania aplikacji można ją zatrzymać ręcznie ( `Ctrl + C` ) lub poczekać na przekroczenie limitu czasu.

#### <a name="what-events-to-collect"></a>Jakie zdarzenia należy zebrać

Podczas zbierania zdarzeń są dostępne trzy opcje:
1. Użyj przełącznika, `-CollectSdkEvents` Aby zbierać zdarzenia emitowane z zestawu SDK Application Insights.
2. Użyj przełącznika, `-CollectRedfieldEvents` Aby zbierać zdarzenia emitowane przez Monitor stanu i środowisko uruchomieniowe Redfield. Te dzienniki są przydatne podczas diagnozowania usług IIS i uruchamiania aplikacji.
3. Użyj obu przełączników do zbierania obu typów zdarzeń.
4. Domyślnie, jeśli nie określono żadnego przełącznika, zostaną zebrane oba typy zdarzeń.


### <a name="parameters"></a>Parametry

#### <a name="-maxdurationinminutes"></a>-MaxDurationInMinutes
**Obowiązkowe.** Użyj tego parametru, aby określić, jak długo ten skrypt ma zbierać zdarzenia. Wartość domyślna to 5 minut.

#### <a name="-logdirectory"></a>-LogDirectory
**Obowiązkowe.** Użyj tego przełącznika, aby ustawić katalog wyjściowy pliku ETL. Domyślnie ten plik zostanie utworzony w katalogu modułów programu PowerShell. Pełna ścieżka zostanie wyświetlona podczas wykonywania skryptu.


#### <a name="-collectsdkevents"></a>-CollectSdkEvents
**Obowiązkowe.** Ten przełącznik umożliwia zbieranie zdarzeń Application Insights SDK.

#### <a name="-collectredfieldevents"></a>-CollectRedfieldEvents
**Obowiązkowe.** Ten przełącznik służy do zbierania zdarzeń z monitor stanu i środowiska uruchomieniowego Redfield.

#### <a name="-verbose"></a>-Verbose
**Wspólny parametr.** Ten przełącznik umożliwia wyprowadzanie szczegółowych dzienników.



### <a name="output"></a>Dane wyjściowe


#### <a name="example-of-application-startup-logs"></a>Przykład dzienników uruchamiania aplikacji
```powershell
PS C:\Windows\system32> Start-ApplicationInsightsMonitoringTrace -ColectRedfieldEvents
Starting...
Log File: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\logs\20190627_144217_ApplicationInsights_ETW_Trace.etl
Tracing enabled, waiting for events.
Tracing will timeout in 5 minutes. Press CTRL+C to cancel.

2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftAppInsights_ManagedHttpModulePath='C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll', MicrosoftAppInsights_ManagedHttpModuleType='Microsoft.ApplicationInsights.RedfieldIISModule.RedfieldIISModule'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftDiagnosticServices_ManagedHttpModulePath2='', MicrosoftDiagnosticServices_ManagedHttpModuleType2=''
2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Environment variable 'MicrosoftDiagnosticServices_ManagedHttpModulePath2' or 'MicrosoftDiagnosticServices_ManagedHttpModuleType2' is null, skipping managed dll loading
2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace MulticastHttpModule.constructor, success, 70 ms
2:42:31 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace Current assembly 'Microsoft.ApplicationInsights.RedfieldIISModule, Version=2.8.18.27202, Culture=neutral, PublicKeyToken=f23a46de0be5d6f3' location 'C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace Matched filter '.*'~'STATUSMONITORTE', '.*'~'DemoWithSql'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace Lightup assembly calculated path: 'C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.Redfield.Lightup.dll'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-FrameworkLightup Trace Loaded applicationInsights.config from assembly's resource Microsoft.ApplicationInsights.Redfield.Lightup, Version=2.8.18.27202, Culture=neutral, PublicKeyToken=f23a46de0be5d6f3/Microsoft.ApplicationInsights.Redfield.Lightup.ApplicationInsights-recommended.config
2:42:34 PM EVENT: Microsoft-ApplicationInsights-FrameworkLightup Trace Successfully attached ApplicationInsights SDK
2:42:34 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace RedfieldIISModule.LoadLightupAssemblyAndGetLightupHttpModuleClass, success, 2687 ms
2:42:34 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace RedfieldIISModule.CreateAndInitializeApplicationInsightsHttpModules(lightupHttpModuleClass), success
2:42:34 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace ManagedHttpModuleHelper, multicastHttpModule.Init() success, 3288 ms
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftAppInsights_ManagedHttpModulePath='C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll', MicrosoftAppInsights_ManagedHttpModuleType='Microsoft.ApplicationInsights.RedfieldIISModule.RedfieldIISModule'
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftDiagnosticServices_ManagedHttpModulePath2='', MicrosoftDiagnosticServices_ManagedHttpModuleType2=''
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Environment variable 'MicrosoftDiagnosticServices_ManagedHttpModulePath2' or 'MicrosoftDiagnosticServices_ManagedHttpModuleType2' is null, skipping managed dll loading
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace MulticastHttpModule.constructor, success, 0 ms
2:42:35 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace RedfieldIISModule.CreateAndInitializeApplicationInsightsHttpModules(lightupHttpModuleClass), success
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace ManagedHttpModuleHelper, multicastHttpModule.Init() success, 0 ms
Timeout Reached. Stopping...
```

## <a name="next-steps"></a>Następne kroki

  Wyświetlanie telemetrii:
 - [Poznaj metryki](../essentials/metrics-charts.md) , aby monitorować wydajność i użycie.
- [Wyszukaj zdarzenia i dzienniki](./diagnostic-search.md) , aby zdiagnozować problemy.
- Użyj [analizy](../logs/log-query-overview.md) , aby uzyskać bardziej zaawansowane zapytania.
- [Tworzenie pulpitów nawigacyjnych](./overview-dashboard.md).
 
 Dodawanie kolejnych funkcji telemetrii:
 - [Tworzenie testów sieci Web](monitor-web-app-availability.md), aby upewnić się, że witryna pozostaje aktywna.
- [Dodaj telemetrię klienta sieci Web](./javascript.md) , aby zobaczyć wyjątki z kodu strony sieci Web i włączyć wywołania śledzenia.
- [Dodaj do kodu zestaw SDK Application Insights](./asp-net.md) , aby móc wstawiać wywołania śledzenia i rejestrowania.
 
 Zrób więcej dzięki Application Insights agentowi:
 - Skorzystaj z naszego przewodnika, aby [rozwiązać problemy z](status-monitor-v2-troubleshoot.md) agentem Application Insights.

