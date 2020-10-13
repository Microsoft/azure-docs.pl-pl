---
title: Metryki analityka magazynu platformy Azure (klasyczne)
description: Dowiedz się, jak używać metryk analityka magazynu w usłudze Azure Storage. Dowiedz się więcej o metrykach transakcji i pojemności, sposobie przechowywania metryk, włączaniu metryk i nie tylko.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring, devx-track-csharp
ms.openlocfilehash: 2f3fa755f61d398ce7f0965fba86262c3e3ec863
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89021157"
---
# <a name="azure-storage-analytics-metrics-classic"></a>Metryki analityka magazynu platformy Azure (klasyczne)

Usługa Azure Storage korzysta z rozwiązania analityka magazynu do przechowywania metryk, które obejmują zagregowane statystyki transakcji oraz dane pojemności dotyczące żądań do usługi magazynu. Transakcje są raportowane na poziomie operacji interfejsu API i na poziomie usługi magazynu. Wydajność jest raportowana na poziomie usługi magazynu. Dane metryk mogą służyć do:
- Analizuj użycie usługi magazynu.
- Diagnozuj problemy z żądaniami skierowanymi do usługi Storage.
- Zwiększenie wydajności aplikacji korzystających z usługi.

 Metryki analityka magazynu są domyślnie włączone dla nowych kont magazynu. Metryki można skonfigurować w [Azure Portal](https://portal.azure.com/). Aby uzyskać więcej informacji, zobacz [Monitorowanie konta magazynu w Azure Portal](/azure/storage/storage-monitor-storage-account). Możesz również włączyć analityka magazynu programowo za pośrednictwem interfejsu API REST lub biblioteki klienta. Użyj operacji ustawiania właściwości usługi, aby włączyć analityka magazynu dla każdej usługi.  

> [!NOTE]
> Metryki analityka magazynu są dostępne dla usługi Azure Blob Storage, Azure queue storage, Azure Table Storage i Azure Files.
> Metryki analityka magazynu są teraz metrykami klasycznymi. Zalecamy używanie [metryk magazynu w Azure monitor](monitor-storage.md) zamiast metryk analityka magazynu.

## <a name="transaction-metrics"></a>Metryki transakcji  
 Niezawodny zestaw danych jest rejestrowany w co godzinę lub w minutę interwałów dla każdej usługi magazynu i żądana operacja interfejsu API, która obejmuje wartości procentowe i wychodzące, dostępność, błędy i przydzielone żądania według kategorii. Aby uzyskać pełną listę szczegółów transakcji, zobacz [analityka magazynu metryk tabeli schematu](/rest/api/storageservices/storage-analytics-metrics-table-schema).  

 Dane transakcji są rejestrowane na poziomie usługi i na poziomie operacji interfejsu API. Na poziomie usługi statystyka, która podsumowuje wszystkie żądane operacje interfejsu API, jest zapisywana w jednostce tabeli co godzinę, nawet jeśli żadne żądania nie zostały wysłane do usługi. Na poziomie operacji interfejsu API statystyki są zapisywane tylko w jednostce, jeśli zażądano tej operacji w danej godzinie.  

 Na przykład w przypadku wykonywania operacji **GetBlob** na usłudze blob usługa analityka magazynu metryki rejestruje żądanie i dołącza je do zagregowanych danych dla usługi BLOB Service i operacji **GetBlob** . Jeśli w ciągu godziny nie zażądano żadnej operacji **GetBlob** , jednostka nie jest zapisywana do *$MetricsTransactionsBlob* dla tej operacji.  

 Metryki transakcji są rejestrowane dla żądań użytkowników i żądań wykonywanych przez analityka magazynu samego siebie. Na przykład zarejestrowano żądania analityka magazynu do zapisu dzienników i jednostek tabel.

## <a name="capacity-metrics"></a>Metryki pojemności  

> [!NOTE]
>  Obecnie metryki pojemności są dostępne tylko dla usługi BLOB Service.

 Dane pojemności są rejestrowane codziennie dla usługi BLOB konta magazynu, a dwie jednostki tabeli są zapisywane. Jedna jednostka zawiera statystyki dotyczące danych użytkownika, a drugi zawiera dane statystyczne dotyczące `$logs` kontenera obiektów BLOB używanych przez analityka magazynu. Tabela *$MetricsCapacityBlob* zawiera następujące statystyki:  

- **Pojemność**: ilość miejsca do magazynowania używanego przez usługę BLOB konta magazynu w bajtach.  
- **ContainerCount**: liczba kontenerów obiektów BLOB w usłudze obiektów BLOB konta magazynu.  
- **ObjectCount**: Liczba przekazanych i nieprzydzielonych bloków lub stronicowych obiektów BLOB w usłudze obiektów BLOB konta magazynu.  

  Aby uzyskać więcej informacji na temat metryk pojemności, zobacz [analityka magazynu metryk tabeli schematu](/rest/api/storageservices/storage-analytics-metrics-table-schema).  

## <a name="how-metrics-are-stored"></a>Jak są przechowywane metryki  

 Wszystkie dane metryk dla każdej usługi magazynu są przechowywane w trzech tabelach zarezerwowanych dla tej usługi. Jedna tabela jest dla informacji o transakcji. jedna tabela jest dla informacji o jednej z minut, a druga — informacje o pojemności. Informacje o transakcji i minucie zawierają dane dotyczące żądań i odpowiedzi. Informacje o pojemności składają się z danych użycia magazynu. Metryki godzinowe, metryki minut i pojemności dla usługi BLOB konta magazynu są dostępne w tabelach, które są opisane w poniższej tabeli.  

|Poziom metryk|Nazwy tabel|Obsługiwane w wersjach|  
|-------------------|-----------------|----------------------------|  
|Metryki godzinowe, podstawowa lokalizacja|-$MetricsTransactionsBlob<br />-$MetricsTransactionsTable<br />-$MetricsTransactionsQueue|Wersje wcześniejsze niż 15 sierpnia 2013. Mimo że te nazwy są nadal obsługiwane, zalecamy przełączenie się do korzystania z tabel, które obserwują.|  
|Metryki godzinowe, podstawowa lokalizacja|-$MetricsHourPrimaryTransactionsBlob<br />-$MetricsHourPrimaryTransactionsTable<br />-$MetricsHourPrimaryTransactionsQueue<br />-$MetricsHourPrimaryTransactionsFile|Wszystkie wersje. Obsługa metryk usługi plików jest dostępna tylko w wersji 5 kwietnia 2015 i nowszych.|  
|Metryki minut, lokalizacja podstawowa|-$MetricsMinutePrimaryTransactionsBlob<br />-$MetricsMinutePrimaryTransactionsTable<br />-$MetricsMinutePrimaryTransactionsQueue<br />-$MetricsMinutePrimaryTransactionsFile|Wszystkie wersje. Obsługa metryk usługi plików jest dostępna tylko w wersji 5 kwietnia 2015 i nowszych.|  
|Metryki godzinowe, lokacja dodatkowa|-$MetricsHourSecondaryTransactionsBlob<br />-$MetricsHourSecondaryTransactionsTable<br />-$MetricsHourSecondaryTransactionsQueue|Wszystkie wersje. Replikacja geograficznie nadmiarowa do odczytu musi być włączona.|  
|Metryki minutowe, lokalizacja dodatkowa|-$MetricsMinuteSecondaryTransactionsBlob<br />-$MetricsMinuteSecondaryTransactionsTable<br />-$MetricsMinuteSecondaryTransactionsQueue|Wszystkie wersje. Replikacja geograficznie nadmiarowa do odczytu musi być włączona.|  
|Pojemność (tylko usługa BLOB)|$MetricsCapacityBlob|Wszystkie wersje.|  

 Te tabele są tworzone automatycznie, gdy analityka magazynu jest włączona dla punktu końcowego usługi magazynu. Są one dostępne za pośrednictwem przestrzeni nazw konta magazynu, na przykład `https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")` . Tabele metryk nie są wyświetlane w operacji tworzenia listy i należy uzyskać do nich dostęp bezpośrednio za pośrednictwem nazwy tabeli.

## <a name="enable-metrics-by-using-the-azure-portal"></a>Włącz metryki przy użyciu Azure Portal
Wykonaj następujące kroki, aby włączyć metryki w [Azure Portal](https://portal.azure.com):

1. Przejdź do swojego konta magazynu.
1. W okienku menu wybierz pozycję **Ustawienia diagnostyczne (klasyczne)** .
1. Upewnij się, że **stan** jest ustawiony na wartość **włączone**.
1. Wybierz metryki dla usług, które chcesz monitorować.
1. Określ zasady przechowywania, aby określić, jak długo mają być przechowywane metryki i dane dziennika.
1. Wybierz pozycję **Zapisz**.

[Azure Portal](https://portal.azure.com) obecnie nie umożliwia konfigurowania metryk minut na koncie magazynu. Należy włączyć metryki minut przy użyciu programu PowerShell lub programowo.

## <a name="enable-storage-metrics-by-using-powershell"></a>Włącz metryki magazynu za pomocą programu PowerShell  
Aby skonfigurować metryki magazynu na koncie magazynu przy Azure PowerShell użyciu polecenia cmdlet **Get-AzStorageServiceMetricsProperty** w programie PowerShell, można użyć narzędzia do pobierania bieżących ustawień. Aby zmienić bieżące ustawienia, należy użyć polecenia cmdlet **Set-AzStorageServiceMetricsProperty** .  

Polecenia cmdlet kontrolujące metryki magazynu wykorzystują następujące parametry:  

* **ServiceType**: możliwe wartości to **obiekt BLOB**, **Kolejka**, **tabela**i **plik**.
* **Metrictype**: możliwe wartości to **Hour** i **minuta**.  
* **MetricsLevel**: możliwe wartości to:
   * **Brak**: wyłącza monitorowanie.
   * **Usługa**: zbiera metryki, takie jak ruch przychodzący i wychodzący, dostępność, opóźnienie i procent sukcesu, które są agregowane dla usług obiektów blob, kolejek, tabel i plików.
   * **ServiceAndApi**: oprócz metryk usług zbiera ten sam zestaw metryk dla każdej operacji magazynu w interfejsie API usługi Azure Storage.

Na przykład następujące polecenie przełącza metryki minut dla usługi BLOB Service na koncie magazynu z okresem przechowywania ustawionym na pięć dni: 

> [!NOTE]
> W tym poleceniu założono, że zalogowano się do subskrypcji platformy Azure za pomocą `Connect-AzAccount` polecenia.

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

Informacje o sposobie konfigurowania Azure PowerShell poleceń cmdlet do pracy z subskrypcją platformy Azure i wybierania domyślnego konta magazynu do użycia znajdują się w temacie [Install and configure Azure PowerShell](https://azure.microsoft.com/documentation/articles/install-configure-powershell/).  

## <a name="enable-storage-metrics-programmatically"></a>Programowo Włącz metryki magazynu  
Oprócz używania Azure Portal lub Azure PowerShell poleceń cmdlet do kontrolowania metryk magazynu, można również użyć jednego z interfejsów API usługi Azure Storage. Jeśli na przykład używasz języka .NET, możesz użyć biblioteki klienta usługi Azure Storage.  

Klasy **CloudBlobClient**, **CloudQueueClient**, **CloudTableClient**i **CloudFileClient** mają takie metody jak **SetServiceProperties** i **SetServicePropertiesAsync** , które przyjmują obiekt **serviceproperties** jako parametr. Aby skonfigurować metryki magazynu, można użyć obiektu **serviceproperties** . Na przykład poniższy fragment kodu w języku C# pokazuje, jak zmienić poziom metryk i dni przechowywania dla metryk kolejki godzinowej:  

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.HourMetrics.MetricsLevel = MetricsLevel.Service;  
serviceProperties.HourMetrics.RetentionDays = 10;  

queueClient.SetServiceProperties(serviceProperties);  
```  

Aby uzyskać więcej informacji o korzystaniu z języka .NET do konfigurowania metryk magazynu, zobacz [biblioteki klienta usługi Azure Storage dla platformy .NET](https://msdn.microsoft.com/library/azure/mt347887.aspx).  

Aby uzyskać ogólne informacje na temat konfigurowania metryk magazynu za pomocą interfejsu API REST, zobacz [Włączanie i konfigurowanie analityka magazynu](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics).  

##  <a name="view-storage-metrics"></a>Wyświetl metryki magazynu  
Po skonfigurowaniu metryk analityka magazynu do monitorowania konta magazynu, analityka magazynu rejestruje metryki w zestawie dobrze znanych tabel na koncie magazynu. Można skonfigurować wykresy, aby wyświetlić metryki godzinowe w [Azure Portal](https://portal.azure.com):

1. Przejdź do swojego konta magazynu w [Azure Portal](https://portal.azure.com).
1. Wybierz pozycję **metryki (klasyczne)** w okienku menu dla usługi, której metryki chcesz wyświetlić.
1. Wybierz wykres, który chcesz skonfigurować.
1. W okienku **Edycja wykresu** wybierz **zakres czasu**, **Typ wykresu**i metryki, które mają być wyświetlane na wykresie.

W sekcji **monitorowanie (klasycznej)** okienka menu konta magazynu w Azure Portal można skonfigurować [reguły alertów](#metrics-alerts). Możesz na przykład wysłać alerty e-mail, aby powiadomić Cię, gdy określona Metryka osiągnie określoną wartość.

Jeśli chcesz pobrać metryki dla magazynu długoterminowego lub przeanalizować je lokalnie, musisz użyć narzędzia lub napisać kod w celu odczytania tabel. Należy pobrać metryki minut na potrzeby analizy. Tabele nie są wyświetlane w przypadku wyświetlenia listy wszystkich tabel na koncie magazynu, ale możesz uzyskać do nich dostęp bezpośrednio według nazwy. Wiele narzędzi do przeglądania magazynu jest świadomych tych tabel i umożliwia ich bezpośrednie wyświetlanie. Aby uzyskać listę dostępnych narzędzi, zobacz [narzędzia klienckie usługi Azure Storage](/azure/storage/storage-explorers).

|Metryki|Nazwy tabel|Uwagi| 
|-|-|-|  
|Metryki godzinowe|$MetricsHourPrimaryTransactionsBlob<br /><br /> $MetricsHourPrimaryTransactionsTable<br /><br /> $MetricsHourPrimaryTransactionsQueue<br /><br /> $MetricsHourPrimaryTransactionsFile|W wersjach wcześniejszych niż 15 sierpnia 2013 te tabele były znane jako:<br /><br /> $MetricsTransactionsBlob<br /><br /> $MetricsTransactionsTable<br /><br /> $MetricsTransactionsQueue<br /><br /> Metryki dla usługi plików są dostępne począwszy od wersji 5 kwietnia 2015.|  
|Metryki minut|$MetricsMinutePrimaryTransactionsBlob<br /><br /> $MetricsMinutePrimaryTransactionsTable<br /><br /> $MetricsMinutePrimaryTransactionsQueue<br /><br /> $MetricsMinutePrimaryTransactionsFile|Można ją włączyć tylko przy użyciu programu PowerShell lub programowo.<br /><br /> Metryki dla usługi plików są dostępne począwszy od wersji 5 kwietnia 2015.|  
|Pojemność|$MetricsCapacityBlob|Tylko Blob service.|  

Aby uzyskać szczegółowe informacje o schematach dla tych tabel, zobacz [analityka magazynu metryk tabeli schematu](/rest/api/storageservices/storage-analytics-metrics-table-schema). Następujące przykładowe wiersze zawierają tylko podzbiór dostępnych kolumn, ale ilustrują pewne ważne funkcje, które umożliwiają zapisanie tych metryk przez metryki magazynu:  

|PartitionKey|RowKey|Timestamp|TotalRequests|TotalBillableRequests|TotalIngress|TotalEgress|Dostępność|AverageE2ELatency|AverageServerLatency|PercentSuccess| 
|-|-|-|-|-|-|-|-|-|-|-|  
|20140522T1100|Użytkownicy Całą|2014-05-22T11:01:16.7650250 Z|7|7|4003|46801|100|104,4286|6,857143|100|  
|20140522T1100|Użytkownicy QueryEntities|2014-05-22T11:01:16.7640250 Z|5|5|2694|45951|100|143,8|7,8|100|  
|20140522T1100|Użytkownicy QueryEntity|2014-05-22T11:01:16.7650250 Z|1|1|538|633|100|3|3|100|  
|20140522T1100|Użytkownicy UpdateEntity|2014-05-22T11:01:16.7650250 Z|1|1|771|217|100|9|6|100|  

W tym przykładzie danych metryk minuta klucz partycji używa czasu o rozdzielczości minutowej. Klucz wiersza określa typ informacji przechowywanych w wierszu. Informacje składają się z typu dostępu i typu żądania:  

-   Typem dostępu jest **użytkownik** lub **system**, gdzie **użytkownik** odwołuje się do wszystkich żądań użytkowników do usługi magazynu, a **system** odnosi się do żądań wysyłanych przez analityka magazynu.  
-   Typem żądania jest **wszystkie**, w którym przypadku jest to linia podsumowania lub identyfikuje określony interfejs API, taki jak **QueryEntity** lub **UpdateEntity**.  

Te przykładowe dane przedstawiają wszystkie rekordy przez pojedynczą minutę (od 11:10:00), więc liczba żądań **QueryEntities** i liczba żądań **QueryEntity** oraz liczba żądań **UpdateEntity** dodaje do siedmiu. Ta suma jest pokazywana w wierszu **User: ALL** . Analogicznie, można utworzyć średni czas oczekiwania na zakończenie na 104,4286 dla **użytkownika: cały** wiersz przez obliczenie ((143,8 * 5) + 3 + 9)/7.  

## <a name="metrics-alerts"></a>Alerty metryk
Rozważ skonfigurowanie alertów w [Azure Portal](https://portal.azure.com) , aby otrzymywać powiadomienia o ważnych zmianach w zachowaniu usług magazynu. Jeśli użyjesz narzędzia Eksplorator usługi Storage, aby pobrać te dane metryk w formacie rozdzielanym, możesz użyć programu Microsoft Excel do przeanalizowania danych. Aby uzyskać listę dostępnych narzędzi Eksplorator usługi Storage, zobacz [narzędzia klienckie usługi Azure Storage](/azure/storage/storage-explorers). Alerty można skonfigurować w okienku **alertu (klasycznego)** , które jest dostępne w obszarze **monitorowanie (klasyczne)** w okienku menu konto magazynu.

> [!IMPORTANT]
> Może wystąpić opóźnienie między zdarzeniem magazynu i w przypadku rejestrowania odpowiednich danych metryk godzinowych lub minutowych. W przypadku metryk minut, kilka minut danych może być jednocześnie zapisywana. Przyczyną tego problemu może być transakcja z wcześniejszych minut agregowania do transakcji przez bieżącą minutę. Gdy ten problem wystąpi, usługa alertów może nie mieć wszystkich dostępnych danych metryk dla skonfigurowanego interwału alertów, co może prowadzić do nieoczekiwanego uruchamiania alertów.
>

## <a name="access-metrics-data-programmatically"></a>Programowe uzyskiwanie dostępu do danych metryk  
Na poniższej liście przedstawiono przykładowy kod w języku C#, który uzyskuje dostęp do metryk minut dla zakresu minut i wyświetla wyniki w oknie konsoli. Przykładowy kod używa biblioteki klienta usługi Azure Storage w wersji 4. x lub nowszej, która obejmuje klasę **CloudAnalyticsClient** , która upraszcza dostęp do tabel metryk w magazynie.  

```csharp
private static void PrintMinuteMetrics(CloudAnalyticsClient analyticsClient, DateTimeOffset startDateTime, DateTimeOffset endDateTime)  
{  
 // Convert the dates to the format used in the PartitionKey.  
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

     // Filter on "user" transactions after fetching the metrics from Azure Table storage.  
     // (StartsWith is not supported using LINQ with Azure Table storage.)  
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

Żądania odczytu i usuwania danych metryk przez klienta są również obciążane według stawek standardowych. W przypadku skonfigurowania zasad przechowywania danych opłaty nie są naliczone, gdy usługa Azure Storage usunie stare dane metryk. W przypadku usunięcia danych analitycznych Twoje konto jest obciążane za operacje usuwania.  

Opłaty za użycie w tabelach metryk są również naliczane. Poniższe informacje służą do oszacowania ilości pojemności używanej do przechowywania danych metryk:  

-   Jeśli każda usługa korzysta z każdego interfejsu API w każdej usłudze, około 148 KB danych jest przechowywana co godzinę w tabelach transakcji metryk w przypadku włączenia podsumowania poziomu usług i interfejsu API.  
-   Jeśli w każdej godzinie usługa korzysta z każdego interfejsu API w usłudze, około 12 KB danych jest przechowywany co godzinę w tabelach transakcji metryk w przypadku włączenia tylko podsumowania poziomu usługi.  
-   Tabela pojemności dla obiektów BLOB zawiera dwa wiersze, które zostały dodane każdego dnia, pod warunkiem w przypadku dzienników. W tym scenariuszu zakłada się, że każdy dzień tej tabeli zwiększa się o maksymalnie 300 bajtów.

## <a name="next-steps"></a>Następne kroki
* [Monitorowanie konta magazynu](https://www.windowsazure.com/manage/services/storage/how-to-monitor-a-storage-account/)   
* [Schemat tabeli metryk analityka magazynu](/rest/api/storageservices/storage-analytics-metrics-table-schema)   
* [analityka magazynu zarejestrowane operacje i komunikaty o stanie](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)   
* [Rejestrowanie analityka magazynu](storage-analytics-logging.md)
