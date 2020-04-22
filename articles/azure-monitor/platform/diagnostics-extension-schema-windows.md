---
title: Schemat rozszerzenia diagnostyki systemu Windows
description: Odwołanie do schematu konfiguracji rozszerzenia diagnostyki systemu Windows (WAD) w usłudze Azure Monitor.
ms.subservice: diagnostic-extension
ms.topic: reference
author: bwren
ms.author: bwren
ms.date: 01/20/2020
ms.openlocfilehash: c04fc82b8b04e474a656a0849177f7aa5d27b427
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81676431"
---
# <a name="windows-diagnostics-extension-schema"></a>Schemat rozszerzenia diagnostyki systemu Windows
Rozszerzenie diagnostyki platformy Azure jest agentem w usłudze Azure Monitor, który zbiera dane monitorowania z systemu operacyjnego gościa i obciążeń zasobów obliczeniowych platformy Azure. W tym artykule opisano schemat używany do konfiguracji rozszerzenia diagnostyki na maszynach wirtualnych systemu Windows i innych zasobach obliczeniowych.

> [!NOTE]
> Schemat w tym artykule jest prawidłowy dla wersji 1.3 i nowszych (Azure SDK 2.4 i nowsze). Nowsze sekcje konfiguracji są komentowane, aby pokazać, w jakiej wersji zostały dodane. Wersja 1.0 i 1.2 schematu zostały zarchiwizowane i nie są już dostępne. 

## <a name="public-configuration-file-schema"></a>Schemat pliku konfiguracji publicznej

Pobierz definicję schematu publicznego pliku konfiguracji, wykonując następujące polecenie programu PowerShell:  

```powershell  
(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File –Encoding utf8 -FilePath 'C:\temp\WadConfig.xsd'  
```  


## <a name="common-attribute-types"></a>Typowe typy atrybutów  
 **scheduledTransferPeriod** atrybut pojawia się w kilku elementach. Jest to interwał między zaplanowanymi transferami do magazynu zaokrąglone w górę do najbliższej minuty. Wartość jest [XML "Typ danych czasu trwania."](https://www.w3schools.com/xml/schema_dtypes_date.asp)


## <a name="diagnosticsconfiguration-element"></a>Element konfiguracji diagnostyki  
 *Drzewo: Katalog główny — konfiguracja diagnostyczna*

Dodano w wersji 1.3.  

Element najwyższego poziomu pliku konfiguracji diagnostyki.  

**Xmlns atrybutów** — obszar nazw XML dla pliku konfiguracji diagnostyki to:  
`http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration`


|Elementy podrzędne|Opis|  
|--------------------|-----------------|  
|**PublicConfig (Konfig publiczny)**|Wymagany. Zobacz opis w innym miejscu na tej stronie.|  
|**PrywatnyConfig**|Element opcjonalny. Zobacz opis w innym miejscu na tej stronie.|  
|**IsEnabled**|Boolean. Zobacz opis w innym miejscu na tej stronie.|  

## <a name="publicconfig-element"></a>PublicConfig Element  
 *Drzewo: Katalog główny — diagnostykaKonfiguracja - PublicConfig*

 W tym artykule opisano konfigurację diagnostyki publicznej.  

|Elementy podrzędne|Opis|  
|--------------------|-----------------|  
|**Okręg wyborczy WadCfg**|Wymagany. Zobacz opis w innym miejscu na tej stronie.|  
|**StorageAccount**|Nazwa konta usługi Azure Storage do przechowywania danych w. Może być również określony jako parametr podczas wykonywania polecenia cmdlet Set-AzureServiceDiagnosticsExtension.|  
|**Rodzaj magazynu**|Może to być *Tabela,* *Blob*lub *TableAndBlob*. Tabela jest domyślna. Po wybraniu TableAndBlob dane diagnostyczne są zapisywane dwa razy — raz do każdego typu.|  
|**LocalResourceDirectory (LocalResourceDirectory)**|Katalog na maszynie wirtualnej, w którym agent monitorowania przechowuje dane zdarzeń. Jeśli nie, ustaw katalog domyślny:<br /><br /> Dla roli pracownika/sieci web:`C:\Resources\<guid>\directory\<guid>.<RoleName.DiagnosticStore\`<br /><br /> Dla maszyny wirtualnej:`C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<WADVersion>\WAD<WADVersion>`<br /><br /> Wymagane atrybuty to:<br /><br /> - **path** — katalog w systemie, który ma być używany przez usługę Azure Diagnostics.<br /><br /> - **expandEnvironment** — określa, czy zmienne środowiskowe są rozwinięte w nazwie ścieżki.|  

## <a name="wadcfg-element"></a>WadCFG Element  
 *Drzewo: Korzeń - DiagnostykaKonfiguracja - PublicConfig - WadCFG*

 Identyfikuje i konfiguruje dane telemetryczne, które mają być zbierane.  


## <a name="diagnosticmonitorconfiguration-element"></a>DiagnosticMonitorConfiguration Element
 *Drzewo: Katalog główny - DiagnostykaKonfiguracja - PublicConfig - WadCFG - DiagnosticMonitorConfiguration*

 Wymagany

|Atrybuty|Opis|  
|----------------|-----------------|  
| **overallQuotaInMB** | Maksymalna ilość miejsca na dysku lokalnym, które mogą być używane przez różne typy danych diagnostycznych zebranych przez usługę Azure Diagnostics. Ustawieniem domyślnym jest 4096 MB.<br />
|**useProxyServer** | Skonfiguruj usługę Azure Diagnostics tak, aby używała ustawień serwera proxy zgodnie z ustawieniami IE.|
|**Zlewozmywaki** | Dodano w 1.5. Element opcjonalny. Wskazuje lokalizację ujścia, aby również wysyłać dane diagnostyczne dla wszystkich elementów podrzędnych, które obsługują pochłaniacze. Przykładem ujścia jest Usługa Application Insights lub Event Hubs.|  


<br /> <br />

|Elementy podrzędne|Opis|  
|--------------------|-----------------|  
|**CrashDumps**|Zobacz opis w innym miejscu na tej stronie.|  
|**DiagnostykaInfrastrukturaLogi**|Włącz zbieranie dzienników generowanych przez usługę Azure Diagnostics. Dzienniki infrastruktury diagnostycznej są przydatne do rozwiązywania problemów z samym systemem diagnostycznym. Atrybuty opcjonalne to:<br /><br /> - **scheduledTransferLogLevelFilter** — konfiguruje minimalny poziom ważności zebranych dzienników.<br /><br /> - **scheduledTransferPeriod** — interwał między zaplanowanymi transferami do magazynu zaokrągloną w górę do najbliższej minuty. Wartość jest [XML "Typ danych czasu trwania."](https://www.w3schools.com/xml/schema_dtypes_date.asp) |  
|**Katalogi**|Zobacz opis w innym miejscu na tej stronie.|  
|**EtwProvidery**|Zobacz opis w innym miejscu na tej stronie.|  
|**Metryki**|Zobacz opis w innym miejscu na tej stronie.|  
|**Liczniki wydajności**|Zobacz opis w innym miejscu na tej stronie.|  
|**WindowsEventLog (WindowsEventLog)**|Zobacz opis w innym miejscu na tej stronie.|
|**Źródła docker**|Zobacz opis w innym miejscu na tej stronie. |



## <a name="crashdumps-element"></a>CrashDumps Element  
 *Drzewo: Korzeń - DiagnostykaKonfiguracja - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - CrashDumps*

 Włącz zbieranie zrzutów awaryjnych.  

|Atrybuty|Opis|  
|----------------|-----------------|  
|**containerName (Nazwa kontenera)**|Element opcjonalny. Nazwa kontenera obiektów blob na koncie usługi Azure Storage, który ma być używany do przechowywania zrzutów awaryjnych.|  
|**typ wypadki**|Element opcjonalny.  Konfiguruje diagnostykę platformy Azure do zbierania mini lub pełnych zrzutów awaryjnych.|  
|**katalogQuotaPercentage**|Element opcjonalny.  Konfiguruje procent **ogólnejQuotaInMB,** które mają być zarezerwowane dla zrzutów awaryjnych na maszynie Wirtualnej.|  

|Elementy podrzędne|Opis|  
|--------------------|-----------------|  
|**Konfiguracja CrashDumpConfiguration**|Wymagany. Definiuje wartości konfiguracji dla każdego procesu.<br /><br /> Wymagany jest również następujący atrybut:<br /><br /> **processName** — nazwa procesu, dla którego usługa Azure Diagnostics ma zbierać zrzut awaryjny.|  

## <a name="directories-element"></a>Element katalogów
 *Drzewo: Korzeń - DiagnostykaKonfiguracja - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - Katalogi*

 Umożliwia zbieranie zawartości katalogu, dzienniki żądań dostępu i/lub dzienniki żądań dostępu i/lub usługi IIS.  

 Opcjonalny **atrybut scheduledTransferPeriod.** Zobacz wyjaśnienie wcześniej.  

|Elementy podrzędne|Opis|  
|--------------------|-----------------|  
|**Dzienniki IISLogs**|Włączenie tego elementu do konfiguracji umożliwia zbieranie dzienników usług IIS:<br /><br /> **containerName** — nazwa kontenera obiektów blob na koncie usługi Azure Storage, który ma być używany do przechowywania dzienników usług IIS.|   
|**Dzienniki failedRequest**|Włączenie tego elementu w konfiguracji umożliwia zbieranie dzienników o nieudanych żądaniach do witryny lub aplikacji usług IIS. Należy również włączyć opcje śledzenia w **systemie. WebServer** w **Web.config**.|  
|**Datasources**|Lista katalogów do monitorowania.|




## <a name="datasources-element"></a>DataSources Element  
 *Drzewo: Katalogi - DiagnostykaKonfiguracja - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - Katalogi - DataSources*

 Lista katalogów do monitorowania.  

|Elementy podrzędne|Opis|  
|--------------------|-----------------|  
|**Konfiguracja katalogu**|Wymagany. Wymagany atrybut:<br /><br /> **containerName** — nazwa kontenera obiektów blob na koncie usługi Azure Storage, który ma być używany do przechowywania plików dziennika.|  





## <a name="directoryconfiguration-element"></a>Element konfiguracji katalogu  
 *Drzewo: katalogi główne — diagnostykaKonfiguracja - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - Katalogi - Źródła danych - Konfiguracja katalogu*

 Może zawierać **Absolute** lub **LocalResource** element, ale nie oba.  

|Elementy podrzędne|Opis|  
|--------------------|-----------------|  
|**Bezwzględna**|Bezwzględna ścieżka do katalogu do monitorowania. Wymagane są następujące atrybuty:<br /><br /> - **Ścieżka** — ścieżka bezwzględna do katalogu do monitorowania.<br /><br /> - **expandEnvironment** — umożliwia skonfigurowanie, czy zmienne środowiskowe w ścieżce są rozwinięte.|  
|**Lokalne Źródło**|Ścieżka względem zasobu lokalnego do monitorowania. Wymagane atrybuty to:<br /><br /> - **Nazwa** — zasób lokalny zawierający katalog do monitorowania<br /><br /> - **relativePath** - Ścieżka względem name zawierająca katalog do monitorowania|  



## <a name="etwproviders-element"></a>EtwProviders Element  
 *Drzewo: Korzeń - DiagnostykaKonfiguracja - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - EtwProviders*

 Konfiguruje kolekcję zdarzeń ETW z dostawców opartych na eventsource i/lub ETW Manifest.  

|Elementy podrzędne|Opis|  
|--------------------|-----------------|  
|**Konfiguracja EtwEventSourceProvider**|Konfiguruje kolekcję zdarzeń wygenerowanych z [klasy EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx). Wymagany atrybut:<br /><br /> **provider** - Nazwa klasy eventSource zdarzenia.<br /><br /> Atrybuty opcjonalne to:<br /><br /> - **scheduledTransferLogLevelFilter** — minimalny poziom ważności do przeniesienia na konto magazynu.<br /><br /> - **scheduledTransferPeriod** — interwał między zaplanowanymi transferami do magazynu zaokrągloną w górę do najbliższej minuty. Wartość jest [XML "Typ danych czasu trwania."](https://www.w3schools.com/xml/schema_dtypes_date.asp) |  
|**Konfiguracja EtwManifestProvider**|Wymagany atrybut:<br /><br /> **dostawca** — identyfikator GUID dostawcy zdarzeń<br /><br /> Atrybuty opcjonalne to:<br /><br /> - **scheduledTransferLogLevelFilter** — minimalny poziom ważności do przeniesienia na konto magazynu.<br /><br /> - **scheduledTransferPeriod** — interwał między zaplanowanymi transferami do magazynu zaokrągloną w górę do najbliższej minuty. Wartość jest [XML "Typ danych czasu trwania."](https://www.w3schools.com/xml/schema_dtypes_date.asp) |  



## <a name="etweventsourceproviderconfiguration-element"></a>Element konfiguracji EtwEventSourceProvider  
 *Drzewo: Katalog główny - DiagnostykaKonfiguracja - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - EtwEventSourceProviderConfiguration*

 Konfiguruje kolekcję zdarzeń wygenerowanych z [klasy EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx).  

|Elementy podrzędne|Opis|  
|--------------------|-----------------|  
|**Defaultevents**|Opcjonalny atrybut:<br/><br/> **eventDestination** — nazwa tabeli do przechowywania zdarzeń w|  
|**Wydarzenie**|Wymagany atrybut:<br /><br /> **id** - Identyfikator zdarzenia.<br /><br /> Opcjonalny atrybut:<br /><br /> **eventDestination** — nazwa tabeli do przechowywania zdarzeń w|  



## <a name="etwmanifestproviderconfiguration-element"></a>Element konfiguracji EtwManifestProvider  
 *Drzewo: Korzeń - DiagnostykaKonfiguracja - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - EtwProviders - EtwManifestProviderConfiguration*

|Elementy podrzędne|Opis|  
|--------------------|-----------------|  
|**Defaultevents**|Opcjonalny atrybut:<br /><br /> **eventDestination** — nazwa tabeli do przechowywania zdarzeń w|  
|**Wydarzenie**|Wymagany atrybut:<br /><br /> **id** - Identyfikator zdarzenia.<br /><br /> Opcjonalny atrybut:<br /><br /> **eventDestination** — nazwa tabeli do przechowywania zdarzeń w|  



## <a name="metrics-element"></a>Element metryki  
 *Drzewo: Katalog główny - DiagnostykaKonfiguracja - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - Metryki*

 Umożliwia wygenerowanie tabeli licznika wydajności, która jest zoptymalizowana pod kątem szybkich zapytań. Każdy licznik wydajności zdefiniowany w **PerformanceCounters** element jest przechowywany w tabeli Metryki oprócz tabeli Licznik wydajności.  

 Wymagany jest atrybut **resourceId.**  Identyfikator zasobu zestawu skalowania maszyny wirtualnej lub maszyny wirtualnej, w których wdrażasz usługę Azure Diagnostics. Pobierz **identyfikator zasobów** z [witryny Azure portal](https://portal.azure.com). Wybierz **pozycję Przeglądaj** -> **grupy**  ->  zasobów **<nazwę\>**. Kliknij **kafelek Właściwości** i skopiuj wartość z pola **Identyfikator.**  

|Elementy podrzędne|Opis|  
|--------------------|-----------------|  
|**MetrykaSegregacja**|Wymagany atrybut:<br /><br /> **scheduledTransferPeriod** — interwał między zaplanowanymi transferami do magazynu zaokrągloną w górę do najbliższej minuty. Wartość jest [XML "Typ danych czasu trwania."](https://www.w3schools.com/xml/schema_dtypes_date.asp) |  



## <a name="performancecounters-element"></a>PerformanceCounters Element  
 *Drzewo: Katalog główny - DiagnostykaKonfiguracja - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - PerformanceCounters*

 Umożliwia zbieranie liczników wydajności.  

 Opcjonalny atrybut:  

 Opcjonalny **atrybut scheduledTransferPeriod.** Zobacz wyjaśnienie wcześniej.

|Element podrzędny|Opis|  
|-------------------|-----------------|  
|**Konfiguracja performanceCounterConfiguration**|Wymagane są następujące atrybuty:<br /><br /> - **counterSpecifier** - Nazwa licznika wydajności. Na przykład `\Processor(_Total)\% Processor Time`. Aby uzyskać listę liczników wydajności na hoście, uruchom polecenie `typeperf`.<br /><br /> - **sampleRate** - Jak często licznik powinien być pobierany.<br /><br /> Opcjonalny atrybut:<br /><br /> **jednostka** - Jednostka miary licznika. Wartości są dostępne w [klasie UnitType](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.sql.models.unittype?view=azure-dotnet) |
|**Zlewozmywaki** | Dodano w 1.5. Element opcjonalny. Wskazuje lokalizację ujścia, aby również wysyłać dane diagnostyczne. Na przykład usługa Azure Monitor lub usługi Event Hubs.|    




## <a name="windowseventlog-element"></a>WindowsEventLog Element
 *Drzewo: Katalog główny - DiagnostykaKonfiguracja - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - WindowsEventLog*

 Włącza zbieranie dzienników zdarzeń systemu Windows.  

 Opcjonalny **atrybut scheduledTransferPeriod.** Zobacz wyjaśnienie wcześniej.  

|Element podrzędny|Opis|  
|-------------------|-----------------|  
|**Datasource**|Dzienniki zdarzeń systemu Windows do zbierania. Wymagany atrybut:<br /><br /> **name** - XPath kwerendy opisujące zdarzenia systemu Windows do zebrania. Przykład:<br /><br /> `Application!*[System[(Level <=3)]], System!*[System[(Level <=3)]], System!*[System[Provider[@Name='Microsoft Antimalware']]], Security!*[System[(Level <= 3)]`<br /><br /> Aby zebrać wszystkie zdarzenia, określ "*" |
|**Zlewozmywaki** | Dodano w 1.5. Element opcjonalny. Wskazuje lokalizację ujścia, aby również wysyłać dane diagnostyczne dla wszystkich elementów podrzędnych, które obsługują pochłaniacze. Przykładem ujścia jest Usługa Application Insights lub Event Hubs.|  


## <a name="logs-element"></a>Element dzienników  
 *Drzewo: Korzeń - DiagnostykaKonfiguracja - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - Dzienniki*

 Obecny w wersji 1.0 i 1.1. Zaginiony w 1.2. Dodano z powrotem w 1.3.  

 Definiuje konfigurację buforu dla podstawowych dzienników platformy Azure.  

|Atrybut|Typ|Opis|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|**Unsignedint**|Element opcjonalny. Określa maksymalną ilość magazynu systemu plików, która jest dostępna dla określonych danych.<br /><br /> Wartość domyślna to 0.|  
|**scheduledTransferLogLevelFilter**|**Ciąg**|Element opcjonalny. Określa minimalny poziom ważności dla przeniesionych wpisów dziennika. Wartością domyślną jest **Undefined**, która przenosi wszystkie dzienniki. Inne możliwe wartości (w kolejności od większości do **najmniejszych**informacji) to pełne, **informacje,** **ostrzeżenie,** **błąd**i **krytyczne**.|  
|**zaplanowany Okres Transferowy**|**Długość**|Element opcjonalny. Określa interwał między zaplanowanymi transferami danych, zaokrąglany w górę do najbliższej minuty.<br /><br /> Wartość domyślna to PT0S.|  
|**Zlewozmywaki** |**Ciąg**| Dodano w 1.5. Element opcjonalny. Wskazuje lokalizację ujścia, aby również wysyłać dane diagnostyczne. Na przykład usługa Application Insights lub Event Hubs.|  

## <a name="dockersources"></a>Źródła docker
 *Drzewo: Katalog główny — diagnostykaKonfigurowanie — PublicConfig - WadCFG - DiagnosticMonitorConfiguration - DockerSources*

 Dodano w 1.9.

|Nazwa elementu|Opis|  
|------------------|-----------------|  
|**Stats**|Informuje system o zbierać statystyki kontenerów platformy Docker|  

## <a name="sinksconfig-element"></a>Elementy SinksConfig  
 *Drzewo: Korzeń - DiagnostykaKonfiguracja - PublicConfig - WadCFG - SinksConfig*

 Lista lokalizacji do wysyłania danych diagnostycznych i konfiguracji skojarzonej z tymi lokalizacjami.  

|Nazwa elementu|Opis|  
|------------------|-----------------|  
|**Ujście**|Zobacz opis w innym miejscu na tej stronie.|  

## <a name="sink-element"></a>Element zlewu
 *Drzewo: Korzeń - DiagnostykaKonfiguracja - PublicConfig - WadCFG - SinksConfig - Sink*

 Dodano w wersji 1.5.  

 Definiuje lokalizacje do wysyłania danych diagnostycznych. Na przykład usługa usługi Application Insights.  

|Atrybut|Typ|Opis|  
|---------------|----------|-----------------|  
|**Nazwa**|ciąg|Ciąg identyfikujący sinkname.|  

|Element|Typ|Opis|  
|-------------|----------|-----------------|  
|**Application Insights**|ciąg|Używane tylko podczas wysyłania danych do usługi Application Insights. Zawierają klucz instrumentacji dla aktywnego konta usługi Application Insights, do którego masz dostęp.|  
|**Kanały**|ciąg|Po jednym dla każdego dodatkowego filtrowania tego strumienia,|  

## <a name="channels-element"></a>Element kanałów  
 *Drzewo: Korzeń - DiagnostykaKonfiguracja - PublicConfig - WadCFG - SinksConfig - Sink - Kanały*

 Dodano w wersji 1.5.  

 Definiuje filtry dla strumieni danych dziennika przechodzących przez ujście.  

|Element|Typ|Opis|  
|-------------|----------|-----------------|  
|**Channel**|ciąg|Zobacz opis w innym miejscu na tej stronie.|  

## <a name="channel-element"></a>Element kanału
 *Drzewo: Korzeń - DiagnostykaKonfiguracja - PublicConfig - WadCFG - SinksConfig - Umywalka - Kanały - Kanał*

 Dodano w wersji 1.5.  

 Definiuje lokalizacje do wysyłania danych diagnostycznych. Na przykład usługa usługi Application Insights.  

|Atrybuty|Typ|Opis|  
|----------------|----------|-----------------|  
|**Loglevel**|**Ciąg**|Określa minimalny poziom ważności dla przeniesionych wpisów dziennika. Wartością domyślną jest **Undefined**, która przenosi wszystkie dzienniki. Inne możliwe wartości (w kolejności od większości do **najmniejszych**informacji) to pełne, **informacje,** **ostrzeżenie,** **błąd**i **krytyczne**.|  
|**Nazwa**|**Ciąg**|Unikatowa nazwa kanału, do|  


## <a name="privateconfig-element"></a>PrivateConfig Element
 *Drzewo: Katalog główny — diagnostykaKonfiguracja — PrivateConfig*

 Dodano w wersji 1.3.  

 Optional (Opcjonalność)  

 Przechowuje prywatne szczegóły konta magazynu (nazwa, klucz i punkt końcowy). Te informacje są wysyłane do maszyny wirtualnej, ale nie można pobrać z niego.  

|Elementy podrzędne|Opis|  
|--------------------|-----------------|  
|**StorageAccount**|Konto magazynu do użycia. Następujące atrybuty są wymagane<br /><br /> - **nazwa** — nazwa konta magazynu.<br /><br /> - **klucz** — klucz do konta magazynu.<br /><br /> - **punkt końcowy** — punkt końcowy dostępu do konta magazynu. <br /><br /> -**sasToken** (dodano 1.8.1)- Można określić token sygnatury dostępu Współdzielonego zamiast klucza konta magazynu w konfiguracji prywatnej. Jeśli jest pod warunkiem, klucz konta magazynu jest ignorowany. <br />Wymagania dotyczące tokenu sygnatury dostępu Współdzielonego: <br />- Obsługuje tylko token SAS konta <br />- *b*, wymagane są typy usług *t.* <br /> - *a*, *c*, *u*, *w* uprawnienia są wymagane. <br /> - *c*, *o* typy zasobów są wymagane. <br /> - Obsługuje tylko protokół HTTPS <br /> - Czas rozpoczęcia i wygaśnięcia musi być ważny.|  


## <a name="isenabled-element"></a>IsEnabled Element  
 *Drzewo: Katalog główny — konfiguracja diagnostyczna — IsEnabled*

 Boolean. Służy `true` do włączania `false` diagnostyki lub wyłączania diagnostyki.

## <a name="example-configuration"></a>Przykładowa konfiguracja
 Poniżej przedstawiono pełną konfigurację przykładową rozszerzenia diagnostyki systemu Windows wyświetlaną zarówno w języku JSON, jak i XML.

 
### <a name="json"></a>JSON

*PublicConfig* i *PrivateConfig* są oddzielone, ponieważ w większości przypadków użycia JSON są one przekazywane jako różne zmienne. Te przypadki obejmują szablony Menedżera zasobów, program PowerShell i Visual Studio.

> [!NOTE]
> Definicja ujścia usługi Azure Monitor ma dwie właściwości: *identyfikator resourceid* i *region.* Są one wymagane tylko dla klasycznych maszyn wirtualnych i usług klasycznej chmury. Te właściwości nie powinny być używane dla innych zasobów.

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
> Definicja ujścia usługi Azure Monitor prywatnej ma dwie właściwości: *PrincipalId* i *Secret*. Są one wymagane tylko dla klasycznych maszyn wirtualnych i usług klasycznej chmury. Te właściwości nie powinny być używane dla innych zasobów.


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

        <PerformanceCounters scheduledTransferPeriod="PT1M", sinks="AzureMonitorSink">  
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
> Definicja ujścia usługi Azure Monitor jest publicznej konfiguracyjnej ma dwie właściwości: identyfikator resourceid i region. Są one wymagane tylko dla klasycznych maszyn wirtualnych i usług klasycznej chmury. Te właściwości nie powinny być używane dla maszyn wirtualnych Menedżera zasobów lub zestawów skalowania maszyny wirtualnej.
> Istnieje również dodatkowy element konfiguracji prywatnej dla ujścia usługi Azure Monitor, który przechodzi w identyfikatorze dublowany i tajny. Jest to wymagane tylko dla klasycznych maszyn wirtualnych i klasycznych usług w chmurze. W przypadku maszyn wirtualnych usługi Resource Manager i maszyn wirtualnych można wykluczyć definicję usługi Azure Monitor w elemencie konfiguracji prywatnej.
>
