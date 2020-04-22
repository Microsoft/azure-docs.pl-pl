---
title: Odwołanie do interfejsu API usługi Azure Application Insights .Net Agent
description: Odwołanie do interfejsu API agenta usługi Application Insights. Monitoruj działanie witryny bez ponownego rozmieszczania witryny. Współpracuje ze ASP.NET aplikacjami sieci web hostowanymi lokalnie, na maszynach wirtualnych lub na platformie Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 02762c4b3af735eb0b4c19aaf450b2b3a416a2be
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733676"
---
# <a name="azure-monitor-application-insights-agent-api-reference"></a>Odwołanie do interfejsu API agenta usługi Azure Monitor Application Insights

W tym artykule opisano polecenie cmdlet, który jest członkiem [modułu Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!NOTE] 
> - Aby rozpocząć, potrzebny jest klucz oprzyrządowania. Aby uzyskać więcej informacji, zobacz [Tworzenie zasobu](create-new-resource.md#copy-the-instrumentation-key).
> - To polecenie cmdlet wymaga zapoznania się i zaakceptowania naszej licencji i oświadczenia o ochronie prywatności.

> [!IMPORTANT] 
> To polecenie cmdlet wymaga sesji programu PowerShell z uprawnieniami administratora i zasadami wykonywania z podwyższonym poziomem uprawnień. Aby uzyskać więcej informacji, zobacz [Uruchamianie programu PowerShell jako administratora z zasadami wykonywania z podwyższonym poziomem uprawnień](status-monitor-v2-detailed-instructions.md#run-powershell-as-admin-with-an-elevated-execution-policy).
> - To polecenie cmdlet wymaga zapoznania się i zaakceptowania naszej licencji i oświadczenia o ochronie prywatności.
> - Aparat oprzyrządowania dodaje dodatkowe obciążenie i jest domyślnie wyłączony.


## <a name="enable-instrumentationengine"></a>Włącz-InstrumentacjaInżynie

Włącza aparat instrumentacji, ustawiając niektóre klucze rejestru.
Uruchom ponownie usługę IIS, aby zmiany zostały wprowadzone.

Aparat instrumentacji może uzupełniać dane zebrane przez zestawy SDK .NET.
Zbiera zdarzenia i komunikaty, które opisują wykonanie procesu zarządzanego. Te zdarzenia i komunikaty obejmują kody wyników zależności, zlecenia HTTP i [tekst polecenia SQL](asp-net-dependencies.md#advanced-sql-tracking-to-get-full-sql-query).

Włączyć silnik oprzyrządowania, jeśli:
- Monitorowanie jest już włączone za pomocą polecenia cmdlet Enable, ale nie włączyłeś aparatu instrumentacji.
- Aplikacja została ręcznie zaarządowana za pomocą zestawów SDK platformy .NET i ma być zbierana dodatkowa telemetria.

### <a name="examples"></a>Przykłady

```powershell
PS C:\> Enable-InstrumentationEngine
```

### <a name="parameters"></a>Parametry

#### <a name="-acceptlicense"></a>-AcceptLicencja
**Opcjonalne.** Ten przełącznik służy do akceptowania licencji i zasad zachowania poufności informacji w instalacjach bezgłowych.

#### <a name="-verbose"></a>-Pełne
**Wspólny parametr.** Użyj tego przełącznika do wyprowadzania szczegółowych dzienników.

### <a name="output"></a>Dane wyjściowe


##### <a name="example-output-from-successfully-enabling-the-instrumentation-engine"></a>Przykładowy wynik z pomyślnego włączenia aparatu oprzyrządowania

```
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
```

## <a name="enable-applicationinsightsmonitoring"></a>Monitorowanie wysuwające aplikacje enable

Umożliwia bezkodowe dołączanie monitorowania aplikacji usług IIS na komputerze docelowym.

To polecenie cmdlet zmodyfikuje aplikację IISHost.config i ustawi niektóre klucze rejestru.
Utworzy również plik applicationinsights.ikey.config, który definiuje klucz instrumentacji używany przez każdą aplikację.
Usługi IIS załadują RedfieldModule podczas uruchamiania, co spowoduje wstrzyknąć sdk usługi Application Insights do aplikacji podczas uruchamiania aplikacji.
Uruchom ponownie usługę IIS, aby zmiany zostały wprowadzone.

Po włączeniu monitorowania zaleca się użycie [metryk na żywo,](live-stream.md) aby szybko sprawdzić, czy aplikacja wysyła nam dane telemetryczne.

### <a name="examples"></a>Przykłady

#### <a name="example-with-a-single-instrumentation-key"></a>Przykład z jednym kluczem oprzyrządowania
W tym przykładzie wszystkie aplikacje na bieżącym komputerze są przypisane do jednego klucza instrumentacji.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

#### <a name="example-with-an-instrumentation-key-map"></a>Przykład z mapą klucza oprzyrządowania
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


### <a name="parameters"></a>Parametry

#### <a name="-instrumentationkey"></a>-InstrumentationKey
**Wymagany.** Ten parametr służy do poniesienia pojedynczego klucza instrumentacji do użycia przez wszystkie aplikacje na komputerze docelowym.

#### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**Wymagany.** Ten parametr służy do dostarczania wielu kluczy instrumentacji i mapowania kluczy instrumentacji używanych przez każdą aplikację.
Można utworzyć pojedynczy skrypt instalacyjny `MachineFilter`dla kilku komputerów, ustawiając plik .

> [!IMPORTANT]
> Aplikacje będą zgodne z regułami w kolejności, w. Dlatego należy najpierw określić najbardziej szczegółowe reguły i ostatnio najbardziej ogólne reguły.

##### <a name="schema"></a>Schemat
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'}})`

- **MachineFilter** jest wymaganym wyrażeniem języka C# nazwy komputera lub maszyny Wirtualnej.
    - '.*' będzie pasować do wszystkich
    - "ComputerName" będzie pasować tylko do komputerów o dokładnej nazwie.
- **AppFilter** jest wymaganym wyrażeniem języka C# nazwy witryny usługi IIS. Listę witryn na serwerze można uzyskać, uruchamiając polecenie [get-iissite](https://docs.microsoft.com/powershell/module/iisadministration/get-iissite).
    - '.*' będzie pasować do wszystkich
    - "SiteName" będzie pasować tylko do witryny IIS z dokładną określoną nazwą.
- **InstrumentationKey** jest wymagane, aby włączyć monitorowanie aplikacji, które pasują do poprzednich dwóch filtrów.
    - Pozostaw tę wartość null, jeśli chcesz zdefiniować reguły, aby wykluczyć monitorowanie.


#### <a name="-enableinstrumentationengine"></a>-EnableInstrumentationEngine
**Opcjonalne.** Użyj tego przełącznika, aby włączyć aparat instrumentacji do zbierania zdarzeń i komunikatów o tym, co dzieje się podczas wykonywania procesu zarządzanego. Te zdarzenia i komunikaty obejmują kody wyników zależności, zlecenia HTTP i tekst polecenia SQL.

Aparat oprzyrządowania dodaje obciążenie i jest domyślnie wyłączony.

#### <a name="-acceptlicense"></a>-AcceptLicencja
**Opcjonalne.** Ten przełącznik służy do akceptowania licencji i zasad zachowania poufności informacji w instalacjach bezgłowych.

#### <a name="-ignoresharedconfig"></a>-IgnoreSharedConfig
Jeśli masz klaster serwerów sieci web, być może używasz [konfiguracji udostępnionej](https://docs.microsoft.com/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211).
Nie można wstrzyknąć modułu http do tej konfiguracji udostępnionej.
Ten skrypt zakończy się niepowodzeniem z komunikatem, że wymagane są dodatkowe kroki instalacji.
Użyj tego przełącznika, aby zignorować tę kontrolę i kontynuować instalowanie wymagań wstępnych. Aby uzyskać więcej informacji, zobacz [znane konflikty z konfiguracją współużytkową](status-monitor-v2-troubleshoot.md#conflict-with-iis-shared-configuration)

#### <a name="-verbose"></a>-Pełne
**Wspólny parametr.** Ten przełącznik służy do wyświetlania szczegółowych dzienników.

#### <a name="-whatif"></a>-CoIf 
**Wspólny parametr.** Ten przełącznik służy do testowania i sprawdzania poprawności parametrów wejściowych bez włączania monitorowania.

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

## <a name="disable-instrumentationengine"></a>Inwalidacja InstrumentacjaEngyna

Wyłącza aparat instrumentacji, usuwając niektóre klucze rejestru.
Uruchom ponownie usługę IIS, aby zmiany zostały wprowadzone.

### <a name="examples"></a>Przykłady

```powershell
PS C:\> Disable-InstrumentationEngine
```

### <a name="parameters"></a>Parametry 

#### <a name="-verbose"></a>-Pełne
**Wspólny parametr.** Użyj tego przełącznika do wyprowadzania szczegółowych dzienników.

### <a name="output"></a>Dane wyjściowe


##### <a name="example-output-from-successfully-disabling-the-instrumentation-engine"></a>Przykładowy wynik z pomyślnego wyłączenia silnika oprzyrządowania

```powershell
Configuring IIS Environment for instrumentation engine...
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]'
Configuring registry for instrumentation engine...
```

## <a name="disable-applicationinsightsmonitoring"></a>Monitorowanie disable-ApplicationInsights

Wyłącza monitorowanie na komputerze docelowym.
To polecenie cmdlet usunie zmiany w aplikacji IISHost.config i usunie klucze rejestru.

### <a name="examples"></a>Przykłady

```powershell
PS C:\> Disable-ApplicationInsightsMonitoring
```

### <a name="parameters"></a>Parametry 

#### <a name="-verbose"></a>-Pełne
**Wspólny parametr.** Ten przełącznik służy do wyświetlania szczegółowych dzienników.

### <a name="output"></a>Dane wyjściowe


##### <a name="example-output-from-successfully-disabling-monitoring"></a>Przykładowy wynik z pomyślnego wyłączania monitorowania

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

Pobiera plik konfiguracyjny i drukuje wartości do konsoli.

### <a name="examples"></a>Przykłady

```powershell
PS C:\> Get-ApplicationInsightsMonitoringConfig
```

### <a name="parameters"></a>Parametry

Nie są wymagane żadne parametry.

### <a name="output"></a>Dane wyjściowe


##### <a name="example-output-from-reading-the-config-file"></a>Przykładowy wynik z odczytu pliku konfiguracyjnego

```
RedfieldConfiguration:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
```

## <a name="get-applicationinsightsmonitoringstatus"></a>Stan monitorowania aplikacji Get-Application

To polecenie cmdlet zawiera informacje dotyczące rozwiązywania problemów z Monitorem stanu.
Użyj tego polecenia cmdlet, aby zbadać stan monitorowania, wersję modułu programu PowerShell i sprawdzić uruchomiony proces.
To polecenie cmdlet będzie raportował informacje o wersji i informacje o kluczowych plikach wymaganych do monitorowania.

### <a name="examples"></a>Przykłady

#### <a name="example-application-status"></a>Przykład: Stan aplikacji

Uruchom polecenie, `Get-ApplicationInsightsMonitoringStatus` aby wyświetlić stan monitorowania witryn sieci web.

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

W tym przykładzie;
- **Identyfikator maszyny** to anonimowy identyfikator używany do jednoznacznej identyfikacji serwera. Jeśli utworzysz żądanie pomocy technicznej, ten identyfikator będzie potrzebny do znajdowania dzienników serwera.
- **Domyślna witryna sieci Web** została zatrzymana w usługach IIS
- **DemoWebApp111** został uruchomiony w uiświadach iis, ale nie otrzymał żadnych żądań. Ten raport pokazuje, że nie ma uruchomionego procesu (ProcessId: nie znaleziono).
- **DemoWebApp222** jest uruchomiony i jest monitorowany (Instrumented: true). Na podstawie konfiguracji użytkownika, Instrumentation Key xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx123 został dopasowany do tej strony.
- **DemoWebApp333** został ręcznie instrumentowany przy użyciu SDK usługi Application Insights. Monitor stanu wykrył sdk i nie będzie monitorował tej lokacji.


#### <a name="example-powershell-module-information"></a>Przykład: Informacje o module programu PowerShell

Uruchom polecenie, `Get-ApplicationInsightsMonitoringStatus -PowerShellModule` aby wyświetlić informacje o bieżącym module:

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

#### <a name="example-runtime-status"></a>Przykład: Stan środowiska uruchomieniowego

Można sprawdzić proces na komputerze oprzyrządowanym, aby sprawdzić, czy wszystkie biblioteki DLL są ładowane. Jeśli monitorowanie działa, należy załadować co najmniej 12 bibliotek DLL.

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

Domyślnie to polecenie cmdlet będzie raportował stan monitorowania aplikacji sieci web.
Użyj tej opcji, aby przejrzeć, jeśli aplikacja została pomyślnie instrumentowane.
Możesz również przejrzeć, który klucz instrumentacji został dopasowany do Twojej witryny.


#### <a name="-powershellmodule"></a>-PowerShellModule
**Opcjonalnie**. Ten przełącznik służy do raportowania numerów wersji i ścieżek bibliotek DLL wymaganych do monitorowania.
Użyj tej opcji, jeśli chcesz zidentyfikować wersję dowolnej biblioteki DLL, w tym zestaw SDK usługi Application Insights.

#### <a name="-inspectprocess"></a>-InspectProcess

**Opcjonalnie**. Ten przełącznik służy do raportowania, czy iIS jest uruchomiony.
Pobierze również narzędzia zewnętrzne, aby ustalić, czy niezbędne biblioteki DLL są ładowane do środowiska wykonawczego IIS.


Jeśli ten proces nie powiedzie się z jakiegokolwiek powodu, można uruchomić te polecenia ręcznie:
- iisreset.exe /status
- [uchwyt64.exe](https://docs.microsoft.com/sysinternals/downloads/handle) -p w3wp | findstr /I "InstrumentationEngine AI. ApplicationInsights"
- [listdlls64.exe](https://docs.microsoft.com/sysinternals/downloads/listdlls) w3wp | findstr /I "InstrumentationEngine AI ApplicationInsights"


#### <a name="-force"></a>-Force

**Opcjonalnie**. Używany tylko z InspectProcess. Użyj tego przełącznika, aby pominąć monit użytkownika, który pojawia się przed pobraniem dodatkowych narzędzi.


## <a name="set-applicationinsightsmonitoringconfig"></a>Set-ApplicationInsightsMonitoringConfig

Ustawia plik konfiguracyjny bez pełnej ponownej instalacji.
Uruchom ponownie usługę IIS, aby zmiany zostały wprowadzone.

> [!IMPORTANT] 
> To polecenie cmdlet wymaga sesji programu PowerShell z uprawnieniami administratora.


### <a name="examples"></a>Przykłady

#### <a name="example-with-a-single-instrumentation-key"></a>Przykład z jednym kluczem oprzyrządowania
W tym przykładzie wszystkim aplikacjom na bieżącym komputerze zostanie przypisany jeden klucz instrumentacji.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

#### <a name="example-with-an-instrumentation-key-map"></a>Przykład z mapą klucza oprzyrządowania
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

### <a name="parameters"></a>Parametry

#### <a name="-instrumentationkey"></a>-InstrumentationKey
**Wymagany.** Ten parametr służy do poniesienia pojedynczego klucza instrumentacji do użycia przez wszystkie aplikacje na komputerze docelowym.

#### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**Wymagany.** Ten parametr służy do dostarczania wielu kluczy instrumentacji i mapowania kluczy instrumentacji używanych przez każdą aplikację.
Można utworzyć pojedynczy skrypt instalacyjny `MachineFilter`dla kilku komputerów, ustawiając plik .

> [!IMPORTANT]
> Aplikacje będą zgodne z regułami w kolejności, w. Dlatego należy najpierw określić najbardziej szczegółowe reguły i ostatnio najbardziej ogólne reguły.

##### <a name="schema"></a>Schemat
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'})`

- **MachineFilter** jest wymaganym wyrażeniem języka C# nazwy komputera lub maszyny Wirtualnej.
    - '.*' będzie pasować do wszystkich
    - "Nazwa komputera" będzie zgodna tylko z komputerami o określonej nazwie.
- **AppFilter** jest wymaganym wyrażeniem języka C# nazwy komputera lub maszyny Wirtualnej.
    - '.*' będzie pasować do wszystkich
    - "ApplicationName" będzie pasować tylko do aplikacji IIS o określonej nazwie.
- **InstrumentationKey** jest wymagane, aby włączyć monitorowanie aplikacji, które pasują do poprzednich dwóch filtrów.
    - Pozostaw tę wartość null, jeśli chcesz zdefiniować reguły, aby wykluczyć monitorowanie.


#### <a name="-verbose"></a>-Pełne
**Wspólny parametr.** Ten przełącznik służy do wyświetlania szczegółowych dzienników.


### <a name="output"></a>Dane wyjściowe

Domyślnie nie ma danych wyjściowych.

##### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkey"></a>Przykład pełne dane wyjściowe z ustawienia pliku konfiguracyjnego za pośrednictwem -InstrumentationKey

```
VERBOSE: Operation: InstallWithIkey
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

##### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkeymap"></a>Przykład pełne dane wyjściowe z ustawienia pliku konfiguracyjnego za pośrednictwem -InstrumentationKeyMap

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

## <a name="start-applicationinsightsmonitoringtrace"></a>Śledzenie monitorowania aplikacji startowych

Zbiera [zdarzenia ETW](https://docs.microsoft.com/windows/desktop/etw/event-tracing-portal) z środowiska wykonawczego dołączania bez kodu. To polecenie cmdlet jest alternatywą dla uruchamiania [Programu PerfView](https://github.com/microsoft/perfview).

Zebrane zdarzenia zostaną wydrukowane na konsoli w czasie rzeczywistym i zapisane w pliku ETL. Wyjściowy plik ETL może zostać otwarty przez [PerfView](https://github.com/microsoft/perfview) w celu dalszego zbadania.

To polecenie cmdlet będzie działać do momentu osiągnięcia limitu czasu (domyślnie`Ctrl + C`5 minut) lub ręcznego zatrzymania ( ).

### <a name="examples"></a>Przykłady

#### <a name="how-to-collect-events"></a>Jak zbierać wydarzenia

Zwykle prosimy o zbieranie zdarzeń w celu zbadania, dlaczego aplikacja nie jest instrumentowana.

Środowisko wykonawcze dołączania bez kodu będzie emitować zdarzenia ETW podczas uruchamiania usług IIS i uruchamiania aplikacji.

Aby zebrać te zdarzenia:
1. W konsoli cmd z uprawnieniami `iisreset /stop` administratora wykonaj, aby wyłączyć usługi IIS i wszystkie aplikacje internetowe.
2. Wykonaj tę polecenie cmdlet
3. W konsoli cmd z uprawnieniami `iisreset /start` administratora uruchom aby uruchomić usługi IIS.
4. Spróbuj przejść do aplikacji.
5. Po zakończeniu ładowania aplikacji można ją ręcznie`Ctrl + C`zatrzymać ( ) lub poczekać na limit czasu.

#### <a name="what-events-to-collect"></a>Jakie wydarzenia zebrać

Podczas zbierania zdarzeń dostępne są trzy opcje:
1. Użyj przełącznika `-CollectSdkEvents` do zbierania zdarzeń emitowanych z SDK usługi Application Insights.
2. Użyj przełącznika `-CollectRedfieldEvents` do zbierania zdarzeń emitowanych przez Monitor stanu i Środowisko uruchomieniowe Redfield. Te dzienniki są przydatne podczas diagnozowania usług IIS i uruchamiania aplikacji.
3. Użyj obu przełączników, aby zebrać oba typy zdarzeń.
4. Domyślnie, jeśli nie przełącznik jest określony oba typy zdarzeń będą zbierane.


### <a name="parameters"></a>Parametry

#### <a name="-maxdurationinminutes"></a>-MaxDurationInMinutes
**Opcjonalne.** Ten parametr służy do ustawiania czasu zbierania zdarzeń przez ten skrypt. Wartość domyślna to 5 minut.

#### <a name="-logdirectory"></a>-LogDirectory
**Opcjonalne.** Ten przełącznik służy do ustawiania katalogu wyjściowego pliku ETL. Domyślnie ten plik zostanie utworzony w katalogu modułów programu PowerShell. Pełna ścieżka zostanie wyświetlona podczas wykonywania skryptu.


#### <a name="-collectsdkevents"></a>-CollectSdkEvents
**Opcjonalne.** Ten przełącznik służy do zbierania zdarzeń SDK usługi Application Insights.

#### <a name="-collectredfieldevents"></a>-CollectRedfieldNiejściacje
**Opcjonalne.** Ten przełącznik służy do zbierania zdarzeń z Monitora stanu i środowiska wykonawczego Redfield.

#### <a name="-verbose"></a>-Pełne
**Wspólny parametr.** Użyj tego przełącznika do wyprowadzania szczegółowych dzienników.



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
 - [Eksploruj metryki,](../../azure-monitor/app/metrics-explorer.md) aby monitorować wydajność i użycie.
- [Szukaj zdarzeń i dzienników](../../azure-monitor/app/diagnostic-search.md) w celu zdiagnozowania problemów.
- Użyj [analizy](../../azure-monitor/app/analytics.md) dla bardziej zaawansowanych zapytań.
- [Tworzenie pulpitów nawigacyjnych](../../azure-monitor/app/overview-dashboard.md).
 
 Dodawanie kolejnych funkcji telemetrii:
 - [Tworzenie testów sieci Web](monitor-web-app-availability.md), aby upewnić się, że witryna pozostaje aktywna.
- [Dodaj dane telemetryczne klienta sieci Web,](../../azure-monitor/app/javascript.md) aby wyświetlić wyjątki od kodu strony sieci web i włączyć śledzenie wywołań.
- [Dodaj SDK usługi Application Insights do kodu, dzięki](../../azure-monitor/app/asp-net.md) czemu można wstawić śledzenie i rejestrowanie wywołań.
 
 Więcej informacji za pomocą agenta usługi Application Insights:
 - Skorzystaj z naszego [przewodnika,](status-monitor-v2-troubleshoot.md) aby rozwiązać problem z agentem aplikacji Insights.






