---
title: Schemat rozszerzenia diagnostyki systemu Windows
description: Dokumentacja schematu konfiguracji rozszerzenia diagnostyki systemu Windows (funkcji wad) w Azure Monitor.
ms.topic: reference
author: bwren
ms.author: bwren
ms.date: 01/20/2020
ms.openlocfilehash: 70b27fec07d074dadb413d1debb098e23b4d33b3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102428732"
---
# <a name="windows-diagnostics-extension-schema"></a>Schemat rozszerzenia diagnostyki systemu Windows
Diagnostyka Azure Extension to Agent w Azure Monitor, który zbiera dane monitorowania z systemu operacyjnego gościa i obciążeń zasobów obliczeniowych platformy Azure. W tym artykule opisano Schemat używany do konfiguracji rozszerzenia diagnostyki na maszynach wirtualnych z systemem Windows i innych zasobów obliczeniowych.

> [!NOTE]
> Schemat w tym artykule jest prawidłowy dla wersji 1,3 i nowszych (zestaw Azure SDK 2,4 i nowsze). W nowszych sekcjach konfiguracyjnych są wyświetlane Komentarze umożliwiające pokazanie, która wersja została dodana. Wersje 1,0 i 1,2 schematu zostały zarchiwizowane i nie są już dostępne. 

## <a name="public-configuration-file-schema"></a>Schemat pliku konfiguracji publicznej

Pobierz definicję schematu pliku konfiguracji publicznej, wykonując następujące polecenie programu PowerShell:  

```powershell  
(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File –Encoding utf8 -FilePath 'C:\temp\WadConfig.xsd'  
```  


## <a name="common-attribute-types"></a>Typy wspólnych atrybutów  
 atrybut **scheduledTransferPeriod** pojawia się w kilku elementach. Jest to interwał między planowanymi transferami do magazynu zaokrąglony do najbliższej minuty. Wartość jest [typem danych "Duration" typu XML ".](https://www.w3schools.com/xml/schema_dtypes_date.asp)


## <a name="diagnosticsconfiguration-element"></a>DiagnosticsConfiguration, element  
 *Drzewo: root-DiagnosticsConfiguration*

Dodano w wersji 1,3.  

Element najwyższego poziomu pliku konfiguracji diagnostyki.  

**Atrybut**  xmlns — przestrzeń nazw XML dla pliku konfiguracji diagnostyki:  
`http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration`


|Elementy podrzędne|Opis|  
|--------------------|-----------------|  
|**PublicConfig**|Wymagane. Zobacz opis w innym miejscu na tej stronie.|  
|**PrivateConfig**|Opcjonalny. Zobacz opis w innym miejscu na tej stronie.|  
|**IsEnabled**|Typu. Zobacz opis w innym miejscu na tej stronie.|  

## <a name="publicconfig-element"></a>PublicConfig, element  
 *Drzewo: root-DiagnosticsConfiguration-PublicConfig*

 Opisuje publiczną konfigurację diagnostyki.  

|Elementy podrzędne|Opis|  
|--------------------|-----------------|  
|**WadCfg**|Wymagane. Zobacz opis w innym miejscu na tej stronie.|  
|**StorageAccount**|Nazwa konta usługi Azure Storage do przechowywania danych. Może być również określony jako parametr podczas wykonywania Set-AzureServiceDiagnosticsExtension polecenia cmdlet.|  
|**StorageType**|Może to być *tabela*, *obiekt BLOB* lub *TableAndBlob*. Tabela jest domyślna. Po wybraniu TableAndBlob dane diagnostyczne są zapisywane dwa razy do każdego typu.|  
|**LocalResourceDirectory**|Katalog na maszynie wirtualnej, w której Agent monitorowania przechowuje dane zdarzenia. Jeśli nie, ustaw, domyślny katalog jest używany:<br /><br /> Dla roli proces roboczy/Sieć Web: `C:\Resources\<guid>\directory\<guid>.<RoleName.DiagnosticStore\`<br /><br /> Dla maszyny wirtualnej: `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<WADVersion>\WAD<WADVersion>`<br /><br /> Wymagane atrybuty:<br /><br /> - **ścieżka** — katalog w systemie, który ma być używany przez Diagnostyka Azure.<br /><br /> - **expandEnvironment** — określa, czy zmienne środowiskowe są rozwinięte w nazwie ścieżki.|  

## <a name="wadcfg-element"></a>WadCFG, element  
 *Drzewo: root-DiagnosticsConfiguration-PublicConfig-WadCFG*

 Identyfikuje i konfiguruje dane telemetryczne, które mają być zbierane.  


## <a name="diagnosticmonitorconfiguration-element"></a>DiagnosticMonitorConfiguration, element
 *Drzewo: root-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration*

 Wymagane

|Atrybuty|Opis|  
|----------------|-----------------|  
| **overallQuotaInMB** | Maksymalna ilość miejsca na dysku lokalnym, która może być używana przez różne typy danych diagnostycznych zbieranych przez Diagnostyka Azure. Ustawienie domyślne to 4096 MB.<br />
|**useProxyServer** | Skonfiguruj Diagnostyka Azure, aby używać ustawień serwera proxy jako ustawionych w ustawieniach programu IE.|
|**ujścia** | Dodano w 1,5. Opcjonalny. Wskazuje lokalizację ujścia, aby wysłać również dane diagnostyczne dla wszystkich elementów podrzędnych, które obsługują ujścia. Przykładem ujścia jest Application Insights lub Event Hubs. Uwaga należy dodać właściwość *ResourceID* w elemencie *Metrics* , jeśli chcesz, aby zdarzenia przekazane do Event Hubs miały identyfikator zasobu. |  


<br /> <br />

|Elementy podrzędne|Opis|  
|--------------------|-----------------|  
|**CrashDumps**|Zobacz opis w innym miejscu na tej stronie.|  
|**DiagnosticInfrastructureLogs**|Włącz zbieranie dzienników generowanych przez Diagnostyka Azure. Dzienniki infrastruktury diagnostycznej są przydatne do rozwiązywania problemów z systemem diagnostyki. Atrybuty opcjonalne:<br /><br /> - **scheduledTransferLogLevelFilter** — konfiguruje minimalny poziom ważności zbieranych dzienników.<br /><br /> - **scheduledTransferPeriod** — interwał między planowanymi transferami do magazynu zaokrąglony do najbliższej minuty. Wartość jest [typem danych "Duration" typu XML ".](https://www.w3schools.com/xml/schema_dtypes_date.asp) |  
|**Katalogu**|Zobacz opis w innym miejscu na tej stronie.|  
|**EtwProviders**|Zobacz opis w innym miejscu na tej stronie.|  
|**Metryki**|Zobacz opis w innym miejscu na tej stronie.|  
|**Liczniki wydajności**|Zobacz opis w innym miejscu na tej stronie.|  
|**WindowsEventLog**|Zobacz opis w innym miejscu na tej stronie.|
|**DockerSources**|Zobacz opis w innym miejscu na tej stronie. |



## <a name="crashdumps-element"></a>Zrzutów awaryjnych formacie, element  
 *Drzewo: root-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-zrzutów awaryjnych formacie*

 Włącz zbieranie zrzutów awaryjnych.  

|Atrybuty|Opis|  
|----------------|-----------------|  
|**containerName**|Opcjonalny. Nazwa kontenera obiektów BLOB na koncie usługi Azure Storage, która będzie używana do przechowywania zrzutów awaryjnych.|  
|**crashDumpType**|Opcjonalny.  Konfiguruje Diagnostyka Azure w celu zbierania danych o minimalnych lub pełnych zrzutach awaryjnych.|  
|**directoryQuotaPercentage**|Opcjonalny.  Określa wartość procentową **overallQuotaInMB** do zarezerwowania na Zrzuty awaryjne na maszynie wirtualnej.|  

|Elementy podrzędne|Opis|  
|--------------------|-----------------|  
|**CrashDumpConfiguration**|Wymagane. Definiuje wartości konfiguracyjne dla każdego procesu.<br /><br /> Wymagany jest również następujący atrybut:<br /><br /> **ProcessName** — nazwa procesu, który ma Diagnostyka Azure zbierać zrzut awaryjny dla.|  

## <a name="directories-element"></a>Elementy katalogów
 *Drzewo: root-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-katalogi*

 Włącza zbieranie zawartości katalogu, Dzienniki żądań dostępu i/lub dzienniki usług IIS.  

 Opcjonalny atrybut **scheduledTransferPeriod** . Zobacz wyjaśnienie wcześniej.  

|Elementy podrzędne|Opis|  
|--------------------|-----------------|  
|**IISLogs**|Uwzględnienie tego elementu w konfiguracji umożliwia zbieranie dzienników usług IIS:<br /><br /> **ContainerName** — nazwa kontenera obiektów BLOB na koncie usługi Azure Storage, która ma być używana do przechowywania dzienników usług IIS.|   
|**FailedRequestLogs**|Uwzględnienie tego elementu w konfiguracji umożliwia zbieranie dzienników dotyczących żądań zakończonych niepowodzeniem do witryny lub aplikacji usług IIS. Należy również włączyć opcje śledzenia w obszarze **System. Serwer WebServer** w **Web.config**.|  
|**Źródła danych**|Lista katalogów do monitorowania.|




## <a name="datasources-element"></a>Element DataSources  
 *Drzewo: root-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-katalogi-DataSources*

 Lista katalogów do monitorowania.  

|Elementy podrzędne|Opis|  
|--------------------|-----------------|  
|**DirectoryConfiguration**|Wymagane. Wymagany atrybut:<br /><br /> **ContainerName** — nazwa kontenera obiektów BLOB na koncie usługi Azure Storage, która ma być używana do przechowywania plików dziennika.|  





## <a name="directoryconfiguration-element"></a>DirectoryConfiguration, element  
 *Drzewo: root-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-katalogi-DataSources-DirectoryConfiguration*

 Może zawierać element **bezwzględny** lub **LocalResource** , ale nie oba jednocześnie.  

|Elementy podrzędne|Opis|  
|--------------------|-----------------|  
|**Bezwzględna**|Ścieżka bezwzględna do katalogu do monitorowania. Wymagane są następujące atrybuty:<br /><br /> - **Ścieżka** — bezwzględna ścieżka do katalogu do monitorowania.<br /><br /> - **expandEnvironment** — określa, czy zmienne środowiskowe w ścieżce są rozwinięte.|  
|**LocalResource**|Ścieżka względna do zasobu lokalnego do monitorowania. Wymagane atrybuty:<br /><br /> - **Nazwa** — zasób lokalny zawierający katalog do monitorowania<br /><br /> - **RelativePath** — ścieżka względna do nazwy zawierającej katalog do monitorowania|  



## <a name="etwproviders-element"></a>EtwProviders, element  
 *Drzewo: root-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-EtwProviders*

 Konfiguruje kolekcję zdarzeń ETW z dostawców EventSource i/lub opartych na manifestach ETW.  

|Elementy podrzędne|Opis|  
|--------------------|-----------------|  
|**EtwEventSourceProviderConfiguration**|Konfiguruje kolekcję zdarzeń wygenerowanych z [klasy EventSource](/dotnet/api/system.diagnostics.tracing.eventsource). Wymagany atrybut:<br /><br /> **dostawca** — nazwa klasy zdarzenia EventSource.<br /><br /> Atrybuty opcjonalne:<br /><br /> - **scheduledTransferLogLevelFilter** — minimalny poziom ważności, który ma zostać przesłany na konto magazynu.<br /><br /> - **scheduledTransferPeriod** — interwał między planowanymi transferami do magazynu zaokrąglony do najbliższej minuty. Wartość jest [typem danych "Duration" typu XML ".](https://www.w3schools.com/xml/schema_dtypes_date.asp) |  
|**EtwManifestProviderConfiguration**|Wymagany atrybut:<br /><br /> **dostawca** — identyfikator GUID dostawcy zdarzeń<br /><br /> Atrybuty opcjonalne:<br /><br /> - **scheduledTransferLogLevelFilter** — minimalny poziom ważności, który ma zostać przesłany na konto magazynu.<br /><br /> - **scheduledTransferPeriod** — interwał między planowanymi transferami do magazynu zaokrąglony do najbliższej minuty. Wartość jest [typem danych "Duration" typu XML ".](https://www.w3schools.com/xml/schema_dtypes_date.asp) |  



## <a name="etweventsourceproviderconfiguration-element"></a>EtwEventSourceProviderConfiguration, element  
 *Drzewo: root-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-EtwProviders-EtwEventSourceProviderConfiguration*

 Konfiguruje kolekcję zdarzeń wygenerowanych z [klasy EventSource](/dotnet/api/system.diagnostics.tracing.eventsource).  

|Elementy podrzędne|Opis|  
|--------------------|-----------------|  
|**DefaultEvents**|Opcjonalny atrybut:<br/><br/> **eventDestination** — nazwa tabeli, w której mają być przechowywane zdarzenia|  
|**Zdarzenie**|Wymagany atrybut:<br /><br /> **Identyfikator** — identyfikator zdarzenia.<br /><br /> Opcjonalny atrybut:<br /><br /> **eventDestination** — nazwa tabeli, w której mają być przechowywane zdarzenia|  



## <a name="etwmanifestproviderconfiguration-element"></a>EtwManifestProviderConfiguration, element  
 *Drzewo: root-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-EtwProviders-EtwManifestProviderConfiguration*

|Elementy podrzędne|Opis|  
|--------------------|-----------------|  
|**DefaultEvents**|Opcjonalny atrybut:<br /><br /> **eventDestination** — nazwa tabeli, w której mają być przechowywane zdarzenia|  
|**Zdarzenie**|Wymagany atrybut:<br /><br /> **Identyfikator** — identyfikator zdarzenia.<br /><br /> Opcjonalny atrybut:<br /><br /> **eventDestination** — nazwa tabeli, w której mają być przechowywane zdarzenia|  



## <a name="metrics-element"></a>Element metryk  
 *Drzewo: root-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-Metrics*

 Umożliwia generowanie tabeli liczników wydajności zoptymalizowanej pod kątem szybkich zapytań. Każdy licznik wydajności, który jest zdefiniowany w elemencie **liczniki wydajności** , jest przechowywany w tabeli metryk oprócz tabeli liczników wydajności.  

 Atrybut **ResourceID** jest wymagany.  Identyfikator zasobu maszyny wirtualnej lub zestawu skalowania maszyn wirtualnych, który jest wdrażany Diagnostyka Azure. Pobierz identyfikator **zasobu** z [Azure Portal](https://portal.azure.com). Wybierz kolejno pozycje **Przeglądaj**  ->  **grupy zasobów**  ->  **<nazwa \>**. Kliknij kafelek **Właściwości** i skopiuj wartość z pola **Identyfikator** .  Ta właściwość resourceID służy do wysyłania metryk niestandardowych i dodawania właściwości resourceID do danych wysyłanych do Event Hubs. Uwaga należy dodać właściwość *ResourceID* w elemencie *Metrics* , jeśli chcesz, aby zdarzenia przekazane do Event Hubs miały identyfikator zasobu.

|Elementy podrzędne|Opis|  
|--------------------|-----------------|  
|**MetricAggregation**|Wymagany atrybut:<br /><br /> **scheduledTransferPeriod** — interwał między planowanymi transferami do magazynu zaokrąglony do najbliższej minuty. Wartość jest [typem danych "Duration" typu XML ".](https://www.w3schools.com/xml/schema_dtypes_date.asp) |  



## <a name="performancecounters-element"></a>Liczniki wydajności, element  
 *Drzewo: root-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-liczniki wydajności*

 Włącza Zbieranie liczników wydajności.  

 Opcjonalny atrybut:  

 Opcjonalny atrybut **scheduledTransferPeriod** . Zobacz wyjaśnienie wcześniej.

|Element podrzędny|Opis|  
|-------------------|-----------------|  
|**PerformanceCounterConfiguration**|Wymagane są następujące atrybuty:<br /><br /> - **counterSpecifier** — Nazwa licznika wydajności. Na przykład `\Processor(_Total)\% Processor Time`. Aby uzyskać listę liczników wydajności na hoście, uruchom polecenie `typeperf` .<br /><br /> - **SampleRate** — częstotliwość próbkowania licznika.<br /><br /> Opcjonalny atrybut:<br /><br /> **Unit** — jednostka miary licznika. Wartości są dostępne w [klasie UnitType](/dotnet/api/microsoft.azure.management.sql.models.unittype) |
|**ujścia** | Dodano w 1,5. Opcjonalny. Wskazuje lokalizację ujścia, aby również wysyłać dane diagnostyczne. Na przykład Azure Monitor lub Event Hubs. Uwaga należy dodać właściwość *ResourceID* w elemencie *Metrics* , jeśli chcesz, aby zdarzenia przekazane do Event Hubs miały identyfikator zasobu.|    




## <a name="windowseventlog-element"></a>WindowsEventLog, element
 *Drzewo: root-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-WindowsEventLog*

 Włącza zbieranie dzienników zdarzeń systemu Windows.  

 Opcjonalny atrybut **scheduledTransferPeriod** . Zobacz wyjaśnienie wcześniej.  

|Element podrzędny|Opis|  
|-------------------|-----------------|  
|**DataSource**|Dzienniki zdarzeń systemu Windows do zebrania. Wymagany atrybut:<br /><br /> **Nazwa** — zapytanie XPath opisujące zdarzenia systemu Windows, które mają być zbierane. Na przykład:<br /><br /> `Application!*[System[(Level <=3)]], System!*[System[(Level <=3)]], System!*[System[Provider[@Name='Microsoft Antimalware']]], Security!*[System[(Level <= 3)]`<br /><br /> Aby zebrać wszystkie zdarzenia, określ wartość "*" |
|**ujścia** | Dodano w 1,5. Opcjonalny. Wskazuje lokalizację ujścia, aby wysłać również dane diagnostyczne dla wszystkich elementów podrzędnych, które obsługują ujścia. Przykładem ujścia jest Application Insights lub Event Hubs.|  


## <a name="logs-element"></a>Logs — element  
 *Drzewo: root-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-Logs*

 Obecne w wersji 1,0 i 1,1. Brak w 1,2. Dodano z powrotem do 1,3.  

 Definiuje konfigurację bufora dla podstawowych dzienników platformy Azure.  

|Atrybut|Typ|Opis|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|**unsignedInt**|Opcjonalny. Określa maksymalną ilość magazynu systemu plików, który jest dostępny dla określonych danych.<br /><br /> Wartość domyślna to 0.|  
|**scheduledTransferLogLevelFilter**|**parametry**|Opcjonalny. Określa minimalny poziom ważności wpisów dziennika, które są transferowane. Wartość domyślna to **undefined**, która przenosi wszystkie dzienniki. Inne możliwe wartości (w kolejności od największej do najmniejszej ilości informacji) to **pełny**, **informacyjny**, **ostrzegawczy**, **błąd** i **krytyczny**.|  
|**scheduledTransferPeriod**|**trwania**|Opcjonalny. Określa interwał między planowanymi transferami danych zaokrągloną w górę do najbliższej minuty.<br /><br /> Wartość domyślna to PT0S.|  
|**ujścia** |**ciąg**| Dodano w 1,5. Opcjonalny. Wskazuje lokalizację ujścia, aby również wysyłać dane diagnostyczne. Na przykład Application Insights lub Event Hubs. Uwaga należy dodać właściwość *ResourceID* w elemencie *Metrics* , jeśli chcesz, aby zdarzenia przekazane do Event Hubs miały identyfikator zasobu.|  

## <a name="dockersources"></a>DockerSources
 *Drzewo: root-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-DockerSources*

 Dodano w 1,9.

|Nazwa elementu|Opis|  
|------------------|-----------------|  
|**Statystyki**|Informuje system, aby gromadził dane statystyczne kontenerów platformy Docker|  

## <a name="sinksconfig-element"></a>SinksConfig, element  
 *Drzewo: root-DiagnosticsConfiguration-PublicConfig-WadCFG-SinksConfig*

 Lista lokalizacji, do których mają zostać wysłane dane diagnostyczne oraz konfiguracja skojarzona z tymi lokalizacjami.  

|Nazwa elementu|Opis|  
|------------------|-----------------|  
|**Ujście**|Zobacz opis w innym miejscu na tej stronie.|  

## <a name="sink-element"></a>Element ujścia
 *Drzewo: root-DiagnosticsConfiguration-PublicConfig-WadCFG-SinksConfig-ujścia*

 Dodano w wersji 1,5.  

 Definiuje lokalizacje, do których mają być wysyłane dane diagnostyczne. Na przykład usługa Application Insights.  

|Atrybut|Typ|Opis|  
|---------------|----------|-----------------|  
|**Nazwij**|ciąg|Ciąg identyfikujący obiekt ujścianame.|  

|Element|Typ|Opis|  
|-------------|----------|-----------------|  
|**Application Insights**|ciąg|Używane tylko w przypadku wysyłania danych do Application Insights. Zawiera klucz Instrumentacji dla aktywnego konta Application Insights, do którego masz dostęp.|  
|**Kanały**|ciąg|Jeden dla każdego dodatkowego filtrowania, który przesyła strumieniowo|  

## <a name="channels-element"></a>Element Channels  
 *Drzewo: root-DiagnosticsConfiguration-PublicConfig-WadCFG-SinksConfig-sink-Channels*

 Dodano w wersji 1,5.  

 Definiuje filtry strumieni danych dziennika przechodzących przez ujścia.  

|Element|Typ|Opis|  
|-------------|----------|-----------------|  
|**Kanał**|ciąg|Zobacz opis w innym miejscu na tej stronie.|  

## <a name="channel-element"></a>Element kanału
 *Drzewo: root-DiagnosticsConfiguration-PublicConfig-WadCFG-SinksConfig-ujścia-Channel*

 Dodano w wersji 1,5.  

 Definiuje lokalizacje, do których mają być wysyłane dane diagnostyczne. Na przykład usługa Application Insights.  

|Atrybuty|Typ|Opis|  
|----------------|----------|-----------------|  
|**logLevel**|**ciąg**|Określa minimalny poziom ważności wpisów dziennika, które są transferowane. Wartość domyślna to **undefined**, która przenosi wszystkie dzienniki. Inne możliwe wartości (w kolejności od największej do najmniejszej ilości informacji) to **pełny**, **informacyjny**, **ostrzegawczy**, **błąd** i **krytyczny**.|  
|**Nazwij**|**ciąg**|Unikatowa nazwa kanału, do którego odwołuje się|  


## <a name="privateconfig-element"></a>PrivateConfig, element
 *Drzewo: root-DiagnosticsConfiguration-PrivateConfig*

 Dodano w wersji 1,3.  

 Opcjonalne  

 Przechowuje prywatne szczegóły konta magazynu (nazwa, klucz i punkt końcowy). Te informacje są wysyłane do maszyny wirtualnej, ale nie można z niej pobrać.  

|Elementy podrzędne|Opis|  
|--------------------|-----------------|  
|**StorageAccount**|Konto magazynu do użycia. Wymagane są następujące atrybuty<br /><br /> - **Nazwa** — nazwa konta magazynu.<br /><br /> - **klucz** — klucz do konta magazynu.<br /><br /> - **Endpoint** — punkt końcowy do uzyskiwania dostępu do konta magazynu. <br /><br /> -**sasToken** (dodano 1.8.1) — w konfiguracji prywatnej można określić token SAS zamiast klucza konta magazynu. Jeśli ta wartość jest określona, klucz konta magazynu jest ignorowany. <br />Wymagania dotyczące tokenu sygnatury dostępu współdzielonego: <br />-Obsługuje tylko token SAS konta <br />- *b*, typy usługi *t* są wymagane. <br /> - wymagane są uprawnienia *a*, *c*, *u*, *w* . <br /> - wymagane są typy zasobów *c*, *o* . <br /> — Obsługuje tylko protokół HTTPS <br /> — Czas rozpoczęcia i wygaśnięcia musi być prawidłowy.|  


## <a name="isenabled-element"></a>IsEnabled, element  
 *Drzewo: root-DiagnosticsConfiguration-IsEnabled*

 Typu. Służy `true` do włączania diagnostyki lub `false` wyłączania diagnostyki.

## <a name="example-configuration"></a>Przykładowa konfiguracja
 Poniżej znajduje się kompletna konfiguracja rozszerzenia diagnostyki systemu Windows pokazana zarówno w formacie JSON, jak i XML.

 
### <a name="json"></a>JSON

*PublicConfig* i *PrivateConfig* są rozdzielone, ponieważ w większości przypadków użycia JSON są one przesyłane jako różne zmienne. Te przypadki obejmują Menedżer zasobów szablonów, programu PowerShell i programu Visual Studio.

> [!NOTE]
> Publiczna definicja Azure Monitor konfiguracji ujścia ma dwie właściwości, *ResourceID* i *region*. Są one wymagane tylko w przypadku klasycznych maszyn wirtualnych i klasycznych usług Cloud Services. Właściwość *region* nie powinna być używana w przypadku innych zasobów, właściwość *ResourceID* jest używana na maszynach wirtualnych ARM w celu wypełnienia pola ResourceID w dziennikach przekazanych do Event Hubs.

```json
"PublicConfig" {
    "WadCfg": {
        "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": 10000,
            "DiagnosticInfrastructureLogs": {
                "scheduledTransferLogLevelFilter": "Error"
            },
            "PerformanceCounters": {
                "scheduledTransferPeriod": "PT1M",
                "sinks": "AzureMonitorSink",
                "PerformanceCounterConfiguration": [
                    {
                        "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                        "sampleRate": "PT1M",
                        "unit": "percent"
                    }
                ]
            },
            "Directories": {
                "scheduledTransferPeriod": "PT5M",
                "IISLogs": {
                    "containerName": "iislogs"
                },
                "FailedRequestLogs": {
                    "containerName": "iisfailed"
                },
                "DataSources": [
                    {
                        "containerName": "mynewprocess",
                        "Absolute": {
                            "path": "C:\\MyNewProcess",
                            "expandEnvironment": false
                        }
                    },
                    {
                        "containerName": "badapp",
                        "Absolute": {
                            "path": "%SYSTEMDRIVE%\\BadApp",
                            "expandEnvironment": true
                        }
                    },
                    {
                        "containerName": "goodapp",
                        "LocalResource": {
                            "relativePath": "..\\PeanutButter",
                            "name": "Skippy"
                        }
                    }
                ]
            },
            "EtwProviders": {
                "sinks": "",
                "EtwEventSourceProviderConfiguration": [
                    {
                        "scheduledTransferPeriod": "PT5M",
                        "provider": "MyProviderClass",
                        "Event": [
                            {
                                "id": 0
                            },
                            {
                                "id": 1,
                                "eventDestination": "errorTable"
                            }
                        ],
                        "DefaultEvents": {
                        }
                    }
                ],
                "EtwManifestProviderConfiguration": [
                    {
                        "scheduledTransferPeriod": "PT2M",
                        "scheduledTransferLogLevelFilter": "Information",
                        "provider": "5974b00b-84c2-44bc-9e58-3a2451b4e3ad",
                        "Event": [
                            {
                                "id": 0
                            }
                        ],
                        "DefaultEvents": {
                        }
                    }
                ]
            },
            "WindowsEventLog": {
                "scheduledTransferPeriod": "PT5M",
                "DataSource": [
                    {
                        "name": "System!*[System[Provider[@Name='Microsoft Antimalware']]]"
                    },
                    {
                        "name": "System!*[System[Provider[@Name='NTFS'] and (EventID=55)]]"
                    },
                    {
                        "name": "System!*[System[Provider[@Name='disk'] and (EventID=7 or EventID=52 or EventID=55)]]"
                    }
                ]
            },
            "Logs": {
                "scheduledTransferPeriod": "PT1M",
                "scheduledTransferLogLevelFilter": "Verbose",
                "sinks": "ApplicationInsights.AppLogs"
            },
            "CrashDumps": {
                "directoryQuotaPercentage": 30,
                "dumpType": "Mini",
                "containerName": "wad-crashdumps",
                "CrashDumpConfiguration": [
                    {
                        "processName": "mynewprocess.exe"
                    },
                    {
                        "processName": "badapp.exe"
                    }
                ]
            }
        },
        "SinksConfig": {
            "Sink": [
                {
                    "name": "AzureMonitorSink",
                    "AzureMonitor":
                    {
                        "ResourceId": "{insert resourceId if a classic VM or cloud service, else property not needed}",
                        "Region": "{insert Azure region of resource if a classic VM or cloud service, else property not needed}"
                    }
                },
                {
                    "name": "ApplicationInsights",
                    "ApplicationInsights": "{Insert InstrumentationKey}",
                    "Channels": {
                        "Channel": [
                            {
                                "logLevel": "Error",
                                "name": "Errors"
                            },
                            {
                                "logLevel": "Verbose",
                                "name": "AppLogs"
                            }
                        ]
                    }
                },
                {
                    "name": "EventHub",
                    "EventHub": {
                        "Url": "https://myeventhub-ns.servicebus.windows.net/diageventhub",
                        "SharedAccessKeyName": "SendRule",
                        "usePublisherId": false
                    }
                },
                {
                    "name": "secondaryEventHub",
                    "EventHub": {
                        "Url": "https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub",
                        "SharedAccessKeyName": "SendRule",
                        "usePublisherId": false
                    }
                },
                {
                    "name": "secondaryStorageAccount",
                    "StorageAccount": {
                        "name": "secondarydiagstorageaccount",
                        "endpoint": "https://core.windows.net"
                    }
                }
            ]
        }
    },
    "StorageAccount": "diagstorageaccount",
    "StorageType": "TableAndBlob"
}
```

> [!NOTE]
> Kolekcja prywatnych Azure Monitor konfiguracji ujścia ma dwie właściwości, *PrincipalId* i *Secret*. Są one wymagane tylko w przypadku klasycznych maszyn wirtualnych i klasycznych usług Cloud Services. Tych właściwości nie należy używać w przypadku innych zasobów.


```json
"PrivateConfig" {
    "storageAccountName": "diagstorageaccount",
    "storageAccountKey": "{base64 encoded key}",
    "storageAccountEndPoint": "https://core.windows.net",
    "storageAccountSasToken": "{sas token}",
    "EventHub": {
        "Url": "https://myeventhub-ns.servicebus.windows.net/diageventhub",
        "SharedAccessKeyName": "SendRule",
        "SharedAccessKey": "{base64 encoded key}"
    },
    "AzureMonitorAccount": {
        "ServicePrincipalMeta": {
            "PrincipalId": "{Insert service principal client Id}",
            "Secret": "{Insert service principal client secret}"
        }
    },
    "SecondaryStorageAccounts": {
        "StorageAccount": [
            {
                "name": "secondarydiagstorageaccount",
                "key": "{base64 encoded key}",
                "endpoint": "https://core.windows.net",
                "sasToken": "{sas token}"
            }
        ]
    },
    "SecondaryEventHubs": {
        "EventHub": [
            {
                "Url": "https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub",
                "SharedAccessKeyName": "SendRule",
                "SharedAccessKey": "{base64 encoded key}"
            }
        ]
    }
}

```

### <a name="xml"></a>XML

```xml  
<?xml version="1.0" encoding="utf-8"?>  
<DiagnosticsConfiguration  xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">   
  <PublicConfig>  
    <WadCfg>  
      <DiagnosticMonitorConfiguration overallQuotaInMB="10000">  

        <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="AzureMonitorSink">  
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT1M" unit="percent" />  
        </PerformanceCounters>  

        <Directories scheduledTransferPeriod="PT5M">  
          <IISLogs containerName="iislogs" />  
          <FailedRequestLogs containerName="iisfailed" />  

          <DataSources>  
            <DirectoryConfiguration containerName="mynewprocess">  
              <Absolute path="C:\MyNewProcess" expandEnvironment="false" />  
            </DirectoryConfiguration>  
            <DirectoryConfiguration containerName="badapp">  
              <Absolute path="%SYSTEMDRIVE%\BadApp" expandEnvironment="true" />  
            </DirectoryConfiguration>  
            <DirectoryConfiguration containerName="goodapp">  
              <LocalResource name="Skippy" relativePath="..\PeanutButter"/>  
            </DirectoryConfiguration>  
          </DataSources>  

        </Directories>  

        <EtwProviders>  
          <EtwEventSourceProviderConfiguration   
                       provider="MyProviderClass"   
                       scheduledTransferPeriod="PT5M">  
            <Event id="0"/>  
            <Event id="1" eventDestination="errorTable"/>  
            <DefaultEvents />  
          </EtwEventSourceProviderConfiguration>  
          <EtwManifestProviderConfiguration provider="5974b00b-84c2-44bc-9e58-3a2451b4e3ad" scheduledTransferLogLevelFilter="Information" scheduledTransferPeriod="PT2M">  
            <Event id="0"/>  
            <DefaultEvents eventDestination="defaultTable"/>  
          </EtwManifestProviderConfiguration>  
        </EtwProviders>  

        <WindowsEventLog scheduledTransferPeriod="PT5M">  
          <DataSource name="System!*[System[Provider[@Name='Microsoft Antimalware']]]"/>  
          <DataSource name="System!*[System[Provider[@Name='NTFS'] and (EventID=55)]]" />  
          <DataSource name="System!*[System[Provider[@Name='disk'] and (EventID=7 or EventID=52 or EventID=55)]]" />  
        </WindowsEventLog>  

        <Logs  bufferQuotaInMB="1024"   
             scheduledTransferPeriod="PT1M"   
             scheduledTransferLogLevelFilter="Verbose"   
             sinks="ApplicationInsights.AppLogs"/>  <!-- sinks attribute added in 1.5 -->  

        <CrashDumps containerName="wad-crashdumps" directoryQuotaPercentage="30" dumpType="Mini">  
          <CrashDumpConfiguration processName="mynewprocess.exe" />  
          <CrashDumpConfiguration processName="badapp.exe"/>  
        </CrashDumps>  

        <DockerSources> <!-- Added in 1.9 -->
          <Stats enabled="true" sampleRate="PT1M" scheduledTransferPeriod="PT1M" />
        </DockerSources>

      </DiagnosticMonitorConfiguration>  

      <SinksConfig>   <!-- Added in 1.5 -->  
        <Sink name="AzureMonitorSink">
            <AzureMonitor> <!-- Added in 1.11 -->
                <resourceId>{insert resourceId}</ResourceId> <!-- Parameter only needed for classic VMs and Classic Cloud Services, exclude VMSS and Resource Manager VMs-->
                <Region>{insert Azure region of resource}</Region> <!-- Parameter only needed for classic VMs and Classic Cloud Services, exclude VMSS and Resource Manager VMs -->
            </AzureMonitor>
        </Sink>
        <Sink name="ApplicationInsights">   
          <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>   
          <Channels>   
            <Channel logLevel="Error" name="Errors"  />   
            <Channel logLevel="Verbose" name="AppLogs"  />   
          </Channels>   
        </Sink>   
        <Sink name="EventHub"> <!-- Added in 1.7 -->
          <EventHub Url="https://myeventhub-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" usePublisherId="false" />
        </Sink>
        <Sink name="secondaryEventHub"> <!-- Added in 1.7 -->
          <EventHub Url="https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub" SharedAccessKeyName="SendRule" usePublisherId="false" />
        </Sink>
        <Sink name="secondaryStorageAccount"> <!-- Added in 1.7 -->
          <StorageAccount name="secondarydiagstorageaccount" endpoint="https://core.windows.net" />
        </Sink>
   </SinksConfig>

  </WadCfg>  

  <StorageAccount>diagstorageaccount</StorageAccount>
  <StorageType>TableAndBlob</StorageType> <!-- Added in 1.8 -->  
  </PublicConfig>  

  <PrivateConfig>  <!-- Added in 1.3 -->  
    <StorageAccount name="" key="" endpoint="" sasToken="{sas token}"  />  <!-- sasToken in Private config added in 1.8.1 -->  
    <EventHub Url="https://myeventhub-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" SharedAccessKey="{base64 encoded key}" />

    <AzureMonitorAccount>
        <ServicePrincipalMeta> <!-- Added in 1.11; only needed for classic VMs and Classic cloud services -->
            <PrincipalId>{Insert service principal clientId}</PrincipalId>
            <Secret>{Insert service principal client secret}</Secret>
        </ServicePrincipalMeta>
    </AzureMonitorAccount>

    <SecondaryStorageAccounts>
       <StorageAccount name="secondarydiagstorageaccount" key="{base64 encoded key}" endpoint="https://core.windows.net" sasToken="{sas token}" />
    </SecondaryStorageAccounts>

    <SecondaryEventHubs>
       <EventHub Url="https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub" SharedAccessKeyName="SendRule" SharedAccessKey="{base64 encoded key}" />
    </SecondaryEventHubs>

  </PrivateConfig>  
  <IsEnabled>true</IsEnabled>  
</DiagnosticsConfiguration>  

```  
> [!NOTE]
> Publiczna definicja Azure Monitor konfiguracji ujścia ma dwie właściwości, resourceId i region. Są one wymagane tylko w przypadku klasycznych maszyn wirtualnych i klasycznych usług Cloud Services. Tych właściwości nie należy używać dla Virtual Machines Menedżer zasobów ani do zestawów skalowania maszyn wirtualnych.
> Istnieje również dodatkowy prywatny element konfiguracji dla ujścia Azure Monitor, który przekazuje identyfikator podmiotu zabezpieczeń i klucz tajny. Jest to wymagane tylko w przypadku klasycznych maszyn wirtualnych i klasycznych Cloud Services. Dla Menedżer zasobów maszyn wirtualnych i VMSS definicję Azure Monitor w prywatnym elemencie konfiguracji można wykluczyć.
>
