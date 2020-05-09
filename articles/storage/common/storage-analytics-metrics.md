---
title: Metryki usługi Azure Storage Analytics (klasyczne)
description: Dowiedz się, jak używać metryk w usłudze Azure Storage.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 03e5f1e888680f6020b45f51103e7b5cb6dc86ab
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/01/2020
ms.locfileid: "82692727"
---
# <a name="azure-storage-analytics-metrics-classic"></a>Metryki usługi Azure Storage Analytics (klasyczne)

Analityka magazynu mogą przechowywać metryki, które obejmują zagregowane statystyki transakcji oraz dane pojemności dotyczące żądań do usługi magazynu. Transakcje są raportowane zarówno na poziomie operacji interfejsu API, jak i na poziomie usługi magazynu, a pojemność jest raportowana na poziomie usługi magazynu. Dane metryk umożliwiają analizowanie użycia usługi magazynu, diagnozowanie problemów z żądaniami dotyczącymi usługi Storage oraz Poprawianie wydajności aplikacji korzystających z usługi.  

 Metryki analityka magazynu są domyślnie włączone dla nowych kont magazynu. Metryki można skonfigurować w [Azure Portal](https://portal.azure.com/); Aby uzyskać szczegółowe informacje, zobacz [Monitorowanie konta magazynu w Azure Portal](/azure/storage/storage-monitor-storage-account). Możesz również włączyć analityka magazynu programowo za pośrednictwem interfejsu API REST lub biblioteki klienta. Użyj operacji ustawiania właściwości usługi, aby włączyć analityka magazynu dla każdej usługi.  

> [!NOTE]
> Metryki analityka magazynu są dostępne dla usług obiektów blob, kolejek, tabel i plików.
> Metryki analityka magazynu są teraz metrykami klasycznymi. Firma Microsoft zaleca używanie [metryk magazynu w Azure monitor](monitor-storage.md) zamiast metryk analityka magazynu.

## <a name="transaction-metrics"></a>Metryki transakcji  
 Niezawodny zestaw danych jest rejestrowany w co godzinę lub w minutę interwałów dla każdej usługi magazynu i żądana operacja interfejsu API, w tym ruch przychodzący/wychodzący, dostępność, błędy i wartości procentowe żądań skategoryzowanych. Pełną listę szczegółów transakcji można znaleźć w temacie [schemat tabeli metryk analityka magazynu](/rest/api/storageservices/storage-analytics-metrics-table-schema) .  

 Dane transakcji są rejestrowane na dwóch poziomach — poziom usługi i poziom operacji interfejsu API. Na poziomie usługi Statystyka podsumowująca wszystkie żądane operacje interfejsu API są zapisywane w jednostce tabeli co godzinę, nawet jeśli żadne żądania nie zostały wysłane do usługi. Na poziomie operacji interfejsu API statystyki są zapisywane tylko w jednostce, jeśli zażądano tej operacji w danej godzinie.  

 Na przykład jeśli wykonujesz operację **GetBlob** na Blob service, analityka magazynu metryki będą rejestrować żądanie i uwzględniać je w zagregowanych danych dla zarówno BLOB Service jak i operacji **GetBlob** . Jeśli jednak w ciągu godziny nie zażądano żadnej operacji **GetBlob** , jednostka nie zostanie zapisywana w *$MetricsTransactionsBlob* dla tej operacji.  

 Metryki transakcji są rejestrowane zarówno dla żądań użytkowników, jak i żądań wykonywanych przez analityka magazynu samego siebie. Na przykład zarejestrowano żądania analityka magazynu do zapisu dzienników i jednostek tabel.

## <a name="capacity-metrics"></a>Metryki pojemności  

> [!NOTE]
>  Obecnie metryki pojemności są dostępne tylko dla Blob service.

 Dane pojemności są rejestrowane codziennie dla Blob service konta magazynu, a dwie jednostki tabeli są zapisywane. Jedna jednostka zawiera statystyki dotyczące danych użytkownika, a drugi zawiera dane statystyczne dotyczące `$logs` kontenera obiektów BLOB używanych przez analityka magazynu. Tabela *$MetricsCapacityBlob* zawiera następujące statystyki:  

- **Pojemność**: ilość miejsca do magazynowania używanego przez BLOB Service konta magazynu w bajtach.  
- **ContainerCount**: liczba kontenerów obiektów BLOB na koncie magazynu BLOB Service.  
- **ObjectCount**: Liczba przekazanych i nieprzydzielonych bloków lub stronicowych obiektów BLOB na koncie magazynu BLOB Service.  

  Aby uzyskać więcej informacji na temat metryk pojemności, zobacz [analityka magazynu metryk tabeli schematu](/rest/api/storageservices/storage-analytics-metrics-table-schema).  

## <a name="how-metrics-are-stored"></a>Jak są przechowywane metryki  

 Wszystkie dane metryk dla każdej usługi magazynu są przechowywane w trzech tabelach zarezerwowanych dla tej usługi: jednej tabeli dla informacji o transakcji, jednej tabeli dla informacji o transakcji minutowej i innej tabeli, aby uzyskać informacje o pojemności. Informacje o transakcji i minucie zawierają dane dotyczące żądań i odpowiedzi, a informacje o pojemności składają się z danych użycia magazynu. W tabelach, które są opisane w poniższej tabeli, można uzyskać dostęp do metryk godzinowych, metryk minut i pojemności dla Blob service konta magazynu.  

|Poziom metryk|Nazwy tabel|Obsługiwane w wersjach|  
|-------------------|-----------------|----------------------------|  
|Metryki godzinowe, podstawowa lokalizacja|-$MetricsTransactionsBlob<br />-$MetricsTransactionsTable<br />-$MetricsTransactionsQueue|Wersje wcześniejsze niż 2013-08-15. Mimo że te nazwy są nadal obsługiwane, zaleca się przełączenie do korzystania z tabel wymienionych poniżej.|  
|Metryki godzinowe, podstawowa lokalizacja|-$MetricsHourPrimaryTransactionsBlob<br />-$MetricsHourPrimaryTransactionsTable<br />-$MetricsHourPrimaryTransactionsQueue<br />-$MetricsHourPrimaryTransactionsFile|Wszystkie wersje. Obsługa metryk usługi plików jest dostępna tylko w wersji 2015-04-05 i nowszych.|  
|Metryki minut, lokalizacja podstawowa|-$MetricsMinutePrimaryTransactionsBlob<br />-$MetricsMinutePrimaryTransactionsTable<br />-$MetricsMinutePrimaryTransactionsQueue<br />-$MetricsMinutePrimaryTransactionsFile|Wszystkie wersje. Obsługa metryk usługi plików jest dostępna tylko w wersji 2015-04-05 i nowszych.|  
|Metryki godzinowe, lokacja dodatkowa|-$MetricsHourSecondaryTransactionsBlob<br />-$MetricsHourSecondaryTransactionsTable<br />-$MetricsHourSecondaryTransactionsQueue|Wszystkie wersje. Replikacja geograficznie nadmiarowa do odczytu musi być włączona.|  
|Metryki minutowe, lokalizacja dodatkowa|-$MetricsMinuteSecondaryTransactionsBlob<br />-$MetricsMinuteSecondaryTransactionsTable<br />-$MetricsMinuteSecondaryTransactionsQueue|Wszystkie wersje. Replikacja geograficznie nadmiarowa do odczytu musi być włączona.|  
|Pojemność (tylko Blob service)|$MetricsCapacityBlob|Wszystkie wersje.|  

 Te tabele są tworzone automatycznie, gdy analityka magazynu jest włączona dla punktu końcowego usługi magazynu. Są one dostępne za pośrednictwem przestrzeni nazw konta magazynu, na przykład: `https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")`. Tabele metryk nie są wyświetlane w operacji tworzenia listy i muszą być dostępne bezpośrednio za pośrednictwem nazwy tabeli.  

## <a name="enable-metrics-using-the-azure-portal"></a>Włącz metryki przy użyciu Azure Portal
Wykonaj następujące kroki, aby włączyć metryki w [Azure Portal](https://portal.azure.com):

1. Przejdź do swojego konta magazynu.
1. Wybierz pozycję **Ustawienia diagnostyczne (klasyczne)** z okienka **menu** .
1. Upewnij się, że **stan** jest ustawiony na wartość **włączone**.
1. Wybierz metryki dla usług, które chcesz monitorować.
1. Określ zasady przechowywania, aby określić, jak długo mają być przechowywane metryki i dane dziennika.
1. Wybierz pozycję **Zapisz**.

[Azure Portal](https://portal.azure.com) obecnie nie umożliwia konfigurowania metryk minut na koncie magazynu. należy włączyć metryki minut przy użyciu programu PowerShell lub programowo.

## <a name="enable-storage-metrics-using-powershell"></a>Włącz metryki magazynu przy użyciu programu PowerShell  
Aby skonfigurować metryki magazynu na koncie magazynu przy Azure PowerShell użyciu polecenia cmdlet **Get-AzStorageServiceMetricsProperty** w programie PowerShell, można użyć narzędzia do pobierania bieżących ustawień oraz polecenia cmdlet **Set-AzStorageServiceMetricsProperty** w celu zmiany bieżących ustawień.  

Polecenia cmdlet kontrolujące metryki magazynu wykorzystują następujące parametry:  

* **ServiceType**, możliwa wartość to **obiekt BLOB**, **Kolejka**, **tabela**i **plik**.
* **Metrictype**, możliwe wartości to **Hour** i **minuta**.  
* **MetricsLevel**, możliwe wartości to:
* **Brak**: wyłącza monitorowanie.
* **Usługa**: zbiera metryki, takie jak ruch przychodzący/ruch wychodzący, dostępność, opóźnienie i procent sukcesu, które są agregowane dla usług obiektów blob, kolejek, tabel i plików.
* **ServiceAndApi**: oprócz metryk usług zbiera ten sam zestaw metryk dla każdej operacji magazynu w interfejsie API usługi Azure Storage.

Na przykład następujące polecenie przełącza metryki minut dla usługi BLOB Service na koncie magazynu z okresem przechowywania ustawionym na pięć dni: 

> [!NOTE]
> W tym poleceniu założono, że zalogowano się do subskrypcji platformy `Connect-AzAccount` Azure za pomocą polecenia.

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"

Set-AzStorageServiceMetricsProperty -MetricsType Minute -ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5 -Context $storageAccount.Context
```  

* Zastąp `<resource-group-name>` wartość symbolu zastępczego nazwą grupy zasobów.
        
* Zastąp wartość symbolu zastępczego `<storage-account-name>` nazwą konta magazynu.



Następujące polecenie pobiera bieżące godzinowe poziomy metryk i dni przechowywania dla usługi BLOB na domyślnym koncie magazynu:  

```powershell
Get-AzStorageServiceMetricsProperty -MetricsType Hour -ServiceType Blob -Context $storagecontext.Context
```  

Aby uzyskać informacje dotyczące sposobu konfigurowania Azure PowerShell poleceń cmdlet do pracy z subskrypcją platformy Azure i wybierania domyślnego konta magazynu do użycia, zobacz: [jak zainstalować i skonfigurować Azure PowerShell](https://azure.microsoft.com/documentation/articles/install-configure-powershell/).  

## <a name="enable-storage-metrics-programmatically"></a>Programowo Włącz metryki magazynu  
Oprócz używania Azure Portal lub Azure PowerShell poleceń cmdlet do kontrolowania metryk magazynu, można również użyć jednego z interfejsów API usługi Azure Storage. Jeśli na przykład używasz języka .NET, możesz użyć biblioteki klienta usługi Storage.  

Klasy **CloudBlobClient**, **CloudQueueClient**, **CloudTableClient**i **CloudFileClient** mają takie metody jak **SetServiceProperties** i **SetServicePropertiesAsync** , które przyjmują obiekt **serviceproperties** jako parametr. Aby skonfigurować metryki magazynu, można użyć obiektu **serviceproperties** . Na przykład poniższy fragment kodu w języku C# pokazuje, jak zmienić poziom metryk i dni przechowywania dla metryk kolejki godzinowej:  

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.HourMetrics.MetricsLevel = MetricsLevel.Service;  
serviceProperties.HourMetrics.RetentionDays = 10;  

queueClient.SetServiceProperties(serviceProperties);  
```  

Aby uzyskać więcej informacji o korzystaniu z języka .NET do konfigurowania metryk magazynu, zobacz [Biblioteka klienta usługi Storage dla platformy .NET](https://msdn.microsoft.com/library/azure/mt347887.aspx).  

Aby uzyskać ogólne informacje na temat konfigurowania metryk magazynu za pomocą interfejsu API REST, zobacz [Włączanie i konfigurowanie analityka magazynu](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics).  

##  <a name="viewing-storage-metrics"></a>Wyświetlanie metryk magazynu  
Po skonfigurowaniu metryk analityka magazynu do monitorowania konta magazynu, analityka magazynu rejestruje metryki w zestawie dobrze znanych tabel na koncie magazynu. Można skonfigurować wykresy, aby wyświetlić metryki godzinowe w [Azure Portal](https://portal.azure.com):

1. Przejdź do swojego konta magazynu w [Azure Portal](https://portal.azure.com).
1. Wybierz pozycję **metryki (klasyczne)** w bloku **menu** dla usługi, której metryki chcesz wyświetlić.
1. Kliknij wykres, który chcesz skonfigurować.
1. W bloku **Edycja wykresu** wybierz **zakres czasu**, **Typ wykresu**i metryki, które mają być wyświetlane na wykresie.

W sekcji **monitorowanie (klasycznej)** w bloku menu konta magazynu w Azure Portal można skonfigurować [reguły alertów](#metrics-alerts), takie jak wysyłanie alertów e-mail w celu powiadomienia, gdy określona Metryka osiągnie określoną wartość.

Jeśli chcesz pobrać metryki dla magazynu długoterminowego lub przeanalizować je lokalnie, musisz użyć narzędzia lub napisać kod w celu odczytania tabel. Należy pobrać metryki minut na potrzeby analizy. Tabele nie są wyświetlane, jeśli zostaną wyświetlone wszystkie tabele na koncie magazynu, ale możesz uzyskać do nich dostęp bezpośrednio według nazwy. Wiele narzędzi do przeglądania magazynu jest świadomych tych tabel i umożliwia ich bezpośrednie wyświetlanie (zobacz [narzędzia klienckie usługi Azure Storage](/azure/storage/storage-explorers) , aby uzyskać listę dostępnych narzędzi).

||||  
|-|-|-|  
|**Metryki**|**Nazwy tabel**|**Uwagi**|  
|Metryki godzinowe|$MetricsHourPrimaryTransactionsBlob<br /><br /> $MetricsHourPrimaryTransactionsTable<br /><br /> $MetricsHourPrimaryTransactionsQueue<br /><br /> $MetricsHourPrimaryTransactionsFile|W wersjach wcześniejszych niż 2013-08-15 te tabele były znane jako:<br /><br /> $MetricsTransactionsBlob<br /><br /> $MetricsTransactionsTable<br /><br /> $MetricsTransactionsQueue<br /><br /> Metryki dla usługi plików są dostępne począwszy od wersji 2015-04-05.|  
|Metryki minut|$MetricsMinutePrimaryTransactionsBlob<br /><br /> $MetricsMinutePrimaryTransactionsTable<br /><br /> $MetricsMinutePrimaryTransactionsQueue<br /><br /> $MetricsMinutePrimaryTransactionsFile|Można ją włączyć tylko przy użyciu programu PowerShell lub programowo.<br /><br /> Metryki dla usługi plików są dostępne począwszy od wersji 2015-04-05.|  
|Pojemność|$MetricsCapacityBlob|Tylko Blob service.|  

Wszystkie szczegóły schematów dla tych tabel można znaleźć w [schemacie tabeli metryk analityka magazynu](/rest/api/storageservices/storage-analytics-metrics-table-schema). W przykładowych wierszach poniżej przedstawiono tylko podzestaw dostępnych kolumn, ale zilustrowano kilka ważnych cech sposobu, w jaki metryki magazynu zapisują następujące metryki:  

||||||||||||  
|-|-|-|-|-|-|-|-|-|-|-|  
|**PartitionKey**|**RowKey**|**Sygnatura czasowa**|**TotalRequests**|**TotalBillableRequests**|**TotalIngress**|**TotalEgress**|**Dostępność**|**AverageE2ELatency**|**AverageServerLatency**|**PercentSuccess**|  
|20140522T1100|Użytkownicy Całą|2014-05-22T11:01:16.7650250 Z|7|7|4003|46801|100|104,4286|6,857143|100|  
|20140522T1100|Użytkownicy QueryEntities|2014-05-22T11:01:16.7640250 Z|5|5|2694|45951|100|143,8|7,8|100|  
|20140522T1100|Użytkownicy QueryEntity|2014-05-22T11:01:16.7650250 Z|1|1|538|633|100|3|3|100|  
|20140522T1100|Użytkownicy UpdateEntity|2014-05-22T11:01:16.7650250 Z|1|1|771|217|100|9|6|100|  

W tym przykładowym czasie dane metryk są wykorzystywane przez klucz partycji. Klucz wiersza określa typ informacji przechowywanych w wierszu i składa się z dwóch informacji, typu dostępu i typu żądania:  

-   Typem dostępu jest **użytkownik** lub **system**, gdzie **użytkownik** odwołuje się do wszystkich żądań użytkowników do usługi Storage, a **system** odnosi się do żądań wykonywanych przez analityka magazynu.  

-   Typem żądania jest **wszystko** , co oznacza, że jest wierszem podsumowania lub identyfikuje określony interfejs API, taki jak **QueryEntity** lub **UpdateEntity**.  

Powyższe dane przykładowe pokazują wszystkie rekordy przez pojedynczą minutę (począwszy od 11:10:00), więc liczbę żądań **QueryEntities** i liczbę żądań **QueryEntity** oraz liczbę żądań **UpdateEntity** dodają do siedmiu, która jest sumą pokazywaną przez **użytkownika: wszystkie** wiersze. Analogicznie, można utworzyć średni czas oczekiwania na zakończenie na 104,4286 dla **użytkownika: cały** wiersz przez obliczenie ((143,8 * 5) + 3 + 9)/7.  

## <a name="metrics-alerts"></a>Alerty metryk
Należy rozważyć skonfigurowanie alertów w [Azure Portal](https://portal.azure.com) , aby otrzymywać powiadomienia o ważnych zmianach w zachowaniu usług magazynu. W przypadku pobierania tych danych metryk przy użyciu narzędzia Eksplorator magazynu w formacie rozdzielanym można użyć programu Microsoft Excel do przeanalizowania danych. Zobacz [narzędzia klienckie usługi Azure Storage](/azure/storage/storage-explorers) , aby zapoznać się z listą dostępnych narzędzi Eksploratora magazynu. Alerty można skonfigurować w bloku **alertu (klasycznego)** dostępnym w obszarze **monitorowanie (klasyczne)** w bloku menu konto magazynu.

> [!IMPORTANT]
> Może istnieć opóźnienie między zdarzeniem magazynu i w przypadku rejestrowania odpowiednich danych metryk godzinowych lub minutowych. W przypadku metryk minut można napisać kilka minut danych jednocześnie. Może to prowadzić do transakcji z wcześniejszych minut agregowania do transakcji przez bieżącą minutę. W takim przypadku usługa alertów może nie mieć wszystkich dostępnych danych metryk dla skonfigurowanego interwału alertów, co może prowadzić do nieoczekiwanego uruchamiania alertów.
>

## <a name="accessing-metrics-data-programmatically"></a>Programistyczne uzyskiwanie dostępu do danych metryk  
Na poniższej liście przedstawiono przykładowy kod w języku C#, który uzyskuje dostęp do metryk minut dla zakresu minut i wyświetla wyniki w oknie konsoli. Przykładowy kod używa biblioteki klienta usługi Azure Storage w wersji 4. x lub nowszej, która obejmuje klasę **CloudAnalyticsClient** , która upraszcza dostęp do tabel metryk w magazynie.  

```csharp
private static void PrintMinuteMetrics(CloudAnalyticsClient analyticsClient, DateTimeOffset startDateTime, DateTimeOffset endDateTime)  
{  
 // Convert the dates to the format used in the PartitionKey  
 var start = startDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");  
 var end = endDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");  

 var services = Enum.GetValues(typeof(StorageService));  
 foreach (StorageService service in services)  
 {  
     Console.WriteLine("Minute Metrics for Service {0} from {1} to {2} UTC", service, start, end);  
     var metricsQuery = analyticsClient.CreateMinuteMetricsQuery(service, StorageLocation.Primary);  
     var t = analyticsClient.GetMinuteMetricsTable(service);  
     var opContext = new OperationContext();  
     var query =  
             from entity in metricsQuery  
             // Note, you can't filter using the entity properties Time, AccessType, or TransactionType  
             // because they are calculated fields in the MetricsEntity class.  
             // The PartitionKey identifies the DataTime of the metrics.  
             where entity.PartitionKey.CompareTo(start) >= 0 && entity.PartitionKey.CompareTo(end) <= 0   
             select entity;  

     // Filter on "user" transactions after fetching the metrics from Table Storage.  
     // (StartsWith is not supported using LINQ with Azure Table storage)  
     var results = query.ToList().Where(m => m.RowKey.StartsWith("user"));  
     var resultString = results.Aggregate(new StringBuilder(), (builder, metrics) => builder.AppendLine(MetricsString(metrics, opContext))).ToString();  
     Console.WriteLine(resultString);  
 }  
}  

private static string MetricsString(MetricsEntity entity, OperationContext opContext)  
{  
 var entityProperties = entity.WriteEntity(opContext);  
 var entityString =  
         string.Format("Time: {0}, ", entity.Time) +  
         string.Format("AccessType: {0}, ", entity.AccessType) +  
         string.Format("TransactionType: {0}, ", entity.TransactionType) +  
         string.Join(",", entityProperties.Select(e => new KeyValuePair<string, string>(e.Key.ToString(), e.Value.PropertyAsObject.ToString())));  
 return entityString;  
}  
```  

## <a name="billing-on-storage-metrics"></a>Rozliczanie metryk magazynu  
Żądania zapisu do tworzenia jednostek tabeli dla metryk są naliczone według standardowych stawek związanych ze wszystkimi operacjami usługi Azure Storage.  

Żądania odczytu i usuwania danych metryk przez klienta są również obciążane według stawek standardowych. Jeśli skonfigurowano zasady przechowywania danych, nie są naliczone opłaty za usługę Azure Storage, która usuwa stare dane metryk. Jeśli jednak usuniesz dane analityczne, Twoje konto zostanie obciążone opłatą za operacje usuwania.  

Opłaty za użycie w tabelach metryk są również naliczane. Aby oszacować ilość pojemności używanej do przechowywania danych metryk, można użyć następujących elementów:  

-   W przypadku każdej godziny usługa korzysta z każdego interfejsu API w każdej usłudze, a następnie około 148KB danych jest przechowywana co godzinę w tabelach transakcji metryk, jeśli włączono zarówno podsumowanie na poziomie usługi, jak i interfejsu API.  
-   Jeśli w ciągu każdej godziny usługa korzysta z każdego interfejsu API w usłudze, wówczas około 12KB danych jest przechowywana co godzinę w tabelach transakcji metryk, jeśli włączono opcję tylko podsumowanie na poziomie usługi.  
-   Tabela pojemności dla obiektów BLOB zawiera dwa wiersze, które zostały dodane każdego dnia, pod warunkiem, że zawarto dzienniki. Oznacza to, że w każdym dniu rozmiar tej tabeli rośnie o maksymalnie 300 bajtów.

## <a name="next-steps"></a>Następne kroki
* [Jak monitorować konto magazynu](https://www.windowsazure.com/manage/services/storage/how-to-monitor-a-storage-account/)   
* [Schemat tabeli metryk analityka magazynu](/rest/api/storageservices/storage-analytics-metrics-table-schema)   
* [analityka magazynu zarejestrowane operacje i komunikaty o stanie](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)   
* [Rejestrowanie analityki magazynu](storage-analytics-logging.md)
