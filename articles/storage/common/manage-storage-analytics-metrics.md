---
title: Włącz i Zarządzaj metrykami Azure Storage Analytics (klasyczny) | Microsoft Docs
description: Dowiedz się, jak włączyć, edytować i wyświetlać metryki Azure Storage Analytics.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring
ms.openlocfilehash: 784929e50d25a07ae92cf388be5ac14f6fa820a6
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2021
ms.locfileid: "99221643"
---
# <a name="enable-and-manage-azure-storage-analytics-metrics-classic"></a>Włącz metryki Azure Storage Analytics i zarządzaj nimi (klasyczne)

[Azure Storage Analytics](storage-analytics.md) dostarcza metryki dla wszystkich usług magazynu dla obiektów blob, kolejek i tabel. Za pomocą [Azure Portal](https://portal.azure.com) można konfigurować, które metryki są rejestrowane dla Twojego konta, oraz konfigurować wykresy, które umożliwiają wizualne reprezentacje danych metryk. W tym artykule pokazano, jak włączyć metryki i zarządzać nimi. Aby dowiedzieć się, jak włączyć dzienniki, zobacz [Włączanie i zarządzanie dziennikami Azure Storage Analytics (wersja klasyczna)](manage-storage-analytics-logs.md).

Zalecamy zapoznanie się [Azure monitor z magazynem](../../azure-monitor/insights/storage-insights-overview.md) (wersja zapoznawcza). Jest to funkcja Azure Monitor, która oferuje kompleksowe monitorowanie kont usługi Azure Storage, zapewniając ujednolicony wgląd w wydajność, wydajność i dostępność usług Azure Storage. Nie wymaga to włączania ani konfigurowania niczego i można od razu wyświetlać te metryki ze wstępnie zdefiniowanych wykresów interaktywnych i innych wizualizacji.

> [!NOTE]
> Istnieją koszty związane z badaniem danych monitorowania w Azure Portal. Aby uzyskać więcej informacji, zobacz [Usługa Storage Analytics](storage-analytics.md).
>
> Konta magazynu blokowych obiektów BLOB wydajności w warstwie Premium nie obsługują metryk analityka magazynu. Jeśli chcesz wyświetlić metryki z kontami bloków BLOB wydajności Premium Storage, rozważ użycie [metryk usługi Azure Storage w Azure monitor](../blobs/monitor-blob-storage.md).
>
> Aby uzyskać szczegółowy przewodnik dotyczący używania analityka magazynu i innych narzędzi do identyfikowania, diagnozowania i rozwiązywania problemów związanych z usługą Azure Storage, zobacz [monitorowanie, diagnozowanie i rozwiązywanie problemów z Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md).
>

<a id="Enable-metrics"></a>

## <a name="enable-metrics"></a>Włącz metryki

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. W [Azure Portal](https://portal.azure.com)wybierz pozycję **konta magazynu**, a następnie nazwę konta magazynu, aby otworzyć pulpit nawigacyjny konta.

2. Wybierz pozycję **Ustawienia diagnostyczne (klasyczne)** w sekcji **monitorowanie (klasyczne)** w bloku menu.
   
   ![Zrzut ekranu, który podświetla opcję Ustawienia diagnostyczne (klasyczne) w sekcji Monitorowanie (klasyczne).](./media/manage-storage-analytics-metrics/storage-enable-metrics-00.png)

3. Wybierz **Typ** danych metryk dla każdej **usługi** , którą chcesz monitorować, oraz **zasady przechowywania** danych. Monitorowanie można również wyłączyć, ustawiając **stan** na **wyłączone**.

   > [!div class="mx-imgBorder"]
   > ![Skonfiguruj rejestrowanie w Azure Portal.](./media/manage-storage-analytics-logs/enable-diagnostics.png) 

   Aby ustawić zasady przechowywania danych, przenieś suwak **przechowywanie (dni)** lub wprowadź liczbę dni, po których dane mają zostać zachowane, od 1 do 365. Wartość domyślna dla nowych kont magazynu wynosi siedem dni. Jeśli nie chcesz ustawiać zasad przechowywania, wprowadź wartość zero. Jeśli nie ma zasad przechowywania, można usunąć dane monitorowania.

   > [!WARNING]
   > Metics są przechowywane jako dane na Twoim koncie. Dane metryk mogą być gromadzone na koncie z upływem czasu, co może zwiększyć koszt magazynu. Jeśli dane dotyczące metryk są potrzebne tylko przez niewielki czas, można zmniejszyć koszty, modyfikując zasady przechowywania danych. Nieodświeżone dane metryk (dane starsze niż zasady przechowywania) są usuwane przez system. Zalecamy ustawienie zasad przechowywania w zależności od tego, jak długo chcesz zachować dane metryk dla Twojego konta. Aby uzyskać więcej informacji, zobacz artykuł dotyczący [rozliczeń za magazyn](storage-analytics-metrics.md#billing-on-storage-metrics) .
   >

4. Po zakończeniu konfigurowania monitorowania wybierz pozycję **Zapisz**.

Domyślny zestaw metryk jest wyświetlany na wykresach w bloku **Przegląd** , a także w bloku **metryki (klasyczne)** . Po włączeniu metryk dla usługi może upłynąć do godziny, aby dane były widoczne na wykresach. Możesz wybrać opcję **Edytuj** na dowolnym wykresie metryki, aby skonfigurować metryki, które są wyświetlane na wykresie.

Zbieranie metryk i rejestrowanie można wyłączyć, ustawiając **stan** na **wyłączone**.

> [!NOTE]
> Usługa Azure Storage używa [magazynu tabel](storage-introduction.md#table-storage) do przechowywania metryk dla konta magazynu i przechowuje metryki w tabelach na Twoim koncie. Aby uzyskać więcej informacji, zobacz [Jak są przechowywane metryki](storage-analytics-metrics.md#how-metrics-are-stored).

### <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

1. Otwórz okno poleceń programu Windows PowerShell.

2. Zaloguj się do subskrypcji platformy Azure za pomocą polecenia `Connect-AzAccount` i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie.

   ```powershell
   Connect-AzAccount
   ```

3. Jeśli Twoja tożsamość jest skojarzona z więcej niż jedną subskrypcją, ustaw aktywną subskrypcję.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Zastąp `<subscription-id>` wartość symbolu zastępczego identyfikatorem subskrypcji.

5. Pobierz kontekst konta magazynu, który definiuje konto magazynu, którego chcesz użyć.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * Zastąp `<resource-group-name>` wartość symbolu zastępczego nazwą grupy zasobów.

   * Zastąp wartość symbolu zastępczego `<storage-account-name>` nazwą konta magazynu. 

6. Aby skonfigurować metryki magazynu na koncie magazynu, można użyć programu PowerShell na komputerze lokalnym. Aby zmienić bieżące ustawienia, należy użyć polecenia cmdlet Azure PowerShell **Set-AzStorageServiceMetricsProperty** . 

   Następujące polecenie przełącza metryki minut dla usługi BLOB Service na koncie magazynu z okresem przechowywania ustawionym na pięć dni.

   ```powershell
   Set-AzStorageServiceMetricsProperty -MetricsType Minute -ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5 -Context $ctx
   ```   

   To polecenie cmdlet używa następujących parametrów:  

   - **ServiceType**: możliwe wartości to **obiekt BLOB**, **Kolejka**, **tabela** i **plik**.
   - **Metrictype**: możliwe wartości to **Hour** i **minuta**.  
   - **MetricsLevel**: możliwe wartości to:
      - **Brak**: wyłącza monitorowanie.
      - **Usługa**: zbiera metryki, takie jak ruch przychodzący i wychodzący, dostępność, opóźnienie i procent sukcesu, które są agregowane dla usług obiektów blob, kolejek, tabel i plików.
      - **ServiceAndApi**: oprócz metryk usług zbiera ten sam zestaw metryk dla każdej operacji magazynu w interfejsie API usługi Azure Storage.

   Następujące polecenie pobiera bieżące godzinowe poziomy metryk i dni przechowywania dla usługi BLOB na domyślnym koncie magazynu:  

   ```powershell
   Get-AzStorageServiceMetricsProperty -MetricsType Hour -ServiceType Blob -Context $storagecontext.Context
   ```  

   Informacje o sposobie konfigurowania Azure PowerShell poleceń cmdlet do pracy z subskrypcją platformy Azure i wybierania domyślnego konta magazynu do użycia znajdują się w temacie [Install and configure Azure PowerShell](/powershell/azure/).  

### <a name="net-v12"></a>[V12 .NET](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/Monitoring.cs" id="snippet_EnableDiagnosticLogs":::

Aby uzyskać więcej informacji o korzystaniu z języka .NET do konfigurowania metryk magazynu, zobacz [biblioteki klienta usługi Azure Storage dla platformy .NET](/dotnet/api/overview/azure/storage).  

Aby uzyskać ogólne informacje na temat konfigurowania metryk magazynu za pomocą interfejsu API REST, zobacz [Włączanie i konfigurowanie analityka magazynu](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics). 

### <a name="net-v11"></a>[V11 .NET](#tab/dotnet11)  

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.HourMetrics.MetricsLevel = MetricsLevel.Service;  
serviceProperties.HourMetrics.RetentionDays = 10;  

queueClient.SetServiceProperties(serviceProperties);  
```  

Aby uzyskać więcej informacji o korzystaniu z języka .NET do konfigurowania metryk magazynu, zobacz [biblioteki klienta usługi Azure Storage dla platformy .NET](/dotnet/api/overview/azure/storage).  

Aby uzyskać ogólne informacje na temat konfigurowania metryk magazynu za pomocą interfejsu API REST, zobacz [Włączanie i konfigurowanie analityka magazynu](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics). 

---

<a id="view-metrics"></a>

## <a name="view-metrics-in-a-chart"></a>Wyświetlanie metryk na wykresie

Po skonfigurowaniu metryk analityka magazynu do monitorowania konta magazynu, analityka magazynu rejestruje metryki w zestawie dobrze znanych tabel na koncie magazynu. Można skonfigurować wykresy, aby wyświetlić metryki godzinowe w [Azure Portal](https://portal.azure.com).

Aby określić, które metryki magazynu mają być wyświetlane na wykresie metryk, należy wykonać poniższą procedurę.

1. Zacznij od wyświetlenia wykresu metryki magazynu w Azure Portal. Wykresy można znaleźć w **bloku konta magazynu** i w bloku **metryki (klasyczne)** .

   W tym przykładzie używa następującego wykresu, który jest wyświetlany w **bloku konta magazynu**:

   ![Wybór wykresu w Azure Portal](./media/manage-storage-analytics-metrics/stg-customize-chart-00.png)

2. Kliknij w dowolnym miejscu na wykresie, aby edytować wykres.

3. Następnie wybierz **zakres czasu** metryk, które mają być wyświetlane na wykresie, oraz **usługę** (obiekt BLOB, kolejka, tabela, plik), których metryki chcesz wyświetlić. W tym miejscu są wybrane metryki z zeszłego tygodnia do wyświetlenia dla usługi BLOB Service:

   ![Zakres czasu i wybór usługi w bloku Edytuj wykres](./media/manage-storage-analytics-metrics/storage-edit-metric-time-range.png)

4. Wybierz poszczególne **metryki** , które mają być wyświetlane na wykresie, a następnie kliknij przycisk **OK**.

   ![Wybór poszczególnych metryk w bloku Edytuj wykres](./media/manage-storage-analytics-metrics/storage-edit-metric-selections.png)

Ustawienia wykresu nie wpływają na zbieranie, agregowanie ani przechowywanie danych monitorowania na koncie magazynu.

#### <a name="metrics-availability-in-charts"></a>Dostępność metryk na wykresach

Lista dostępnych metryk zmienia się w zależności od wybranej usługi na liście rozwijanej oraz typu jednostki edytowanego wykresu. Na przykład można wybrać metryki procentowe, takie jak *wzrost percentnetworkerror* i *wzrost percentthrottlingerror* , tylko w przypadku edytowania wykresu, w którym wyświetlane są jednostki w procentach:

![Wykres procentowy błędu żądania w Azure Portal](./media/manage-storage-analytics-metrics/stg-customize-chart-04.png)

#### <a name="metrics-resolution"></a>Rozwiązanie metryk

Metryki wybrane w obszarze **Diagnostyka** określają rozdzielczość metryk, które są dostępne dla Twojego konta:

* Funkcja monitorowania **zagregowanego** zapewnia takie metryki jak ruch przychodzący/wychodzący, dostępność, opóźnienie i procent sukcesu. Te metryki są agregowane z usług obiektów blob, tabel, plików i kolejek.
* **Na interfejs API** zapewnia bardziej precyzyjną rozdzielczość, z metrykami dostępnymi dla poszczególnych operacji magazynu, poza agregacjami na poziomie usług.

## <a name="download-metrics-to-archive-or-analyze-locally"></a>Pobierz metryki do zarchiwizowania lub przeanalizowania lokalnie

Jeśli chcesz pobrać metryki dla magazynu długoterminowego lub przeanalizować je lokalnie, musisz użyć narzędzia lub napisać kod w celu odczytania tabel. Tabele nie są wyświetlane w przypadku wyświetlenia listy wszystkich tabel na koncie magazynu, ale możesz uzyskać do nich dostęp bezpośrednio według nazwy. Wiele narzędzi do przeglądania magazynu jest świadomych tych tabel i umożliwia ich bezpośrednie wyświetlanie. Aby uzyskać listę dostępnych narzędzi, zobacz [narzędzia klienckie usługi Azure Storage](./storage-explorers.md).

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

## <a name="view-metrics-data-programmatically"></a>Programistyczne wyświetlanie danych metryk

Na poniższej liście przedstawiono przykładowy kod w języku C#, który uzyskuje dostęp do metryk minut dla zakresu minut i wyświetla wyniki w oknie konsoli. Przykładowy kod używa biblioteki klienta usługi Azure Storage w wersji 4. x lub nowszej, która obejmuje klasę **CloudAnalyticsClient** , która upraszcza dostęp do tabel metryk w magazynie. 

> [!NOTE]
> Klasa **CloudAnalyticsClient** nie jest uwzględniona w bibliotece klienta usługi Azure Blob Storage V12 dla platformy .NET. W dniu **31 sierpnia 2023** metryki analityka magazynu, nazywane również *metrykami klasycznymi* , zostaną wycofane. Więcej informacji znajdziesz w [oficjalnym ogłoszeniu](https://azure.microsoft.com/updates/azure-storage-classic-metrics-will-be-retired-on-31-august-2023/). Jeśli używasz klasycznych metryk, zalecamy przechodzenie do metryk w Azure Monitor przed tą datą. 

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

<a id="add-metrics-to-dashboard"></a>

## <a name="add-metrics-charts-to-the-portal-dashboard"></a>Dodawanie wykresów metryk do pulpitu nawigacyjnego portalu

Wykresy metryk usługi Azure Storage można dodać do pulpitu nawigacyjnego portalu na dowolnym koncie magazynu.

1. Wybierz pozycję **Edytuj pulpit nawigacyjny** podczas wyświetlania pulpitu nawigacyjnego w [Azure Portal](https://portal.azure.com).
1. W **galerii kafelków** wybierz pozycję **Znajdź kafelki według**  >  **typu**.
1. Wybierz pozycję **Typ**  >  **konta magazynu**.
1. W obszarze **zasoby** wybierz konto magazynu, którego metryki chcesz dodać do pulpitu nawigacyjnego.
1. Wybierz pozycję **Kategorie**  >  **monitorowanie**.
1. Przeciągnij i upuść kafelek wykresu na pulpicie nawigacyjnym dla metryki, która ma być wyświetlana. Powtórz te czynności dla wszystkich metryk, które mają być wyświetlane na pulpicie nawigacyjnym. Na poniższej ilustracji wykres "obiekty blob — łączna liczba żądań" został wyróżniony jako przykład, ale wszystkie wykresy są dostępne do umieszczenia na pulpicie nawigacyjnym.

   ![Galeria kafelków w Azure Portal](./media/manage-storage-analytics-metrics/storage-customize-dashboard.png)
1. Wybierz pozycję **gotowe Dostosowywanie** w górnej części pulpitu nawigacyjnego po zakończeniu dodawania wykresów.

Po dodaniu wykresów do pulpitu nawigacyjnego można je dodatkowo dostosować zgodnie z opisem w temacie dostosowywanie wykresów metryk.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej na temat analityka magazynu, zobacz [analityka magazynu](storage-analytics.md) for analityka magazynu.
* [Skonfiguruj dzienniki analityka magazynu](manage-storage-analytics-logs.md).
* Dowiedz się więcej na temat schematu metryk. Zobacz [analityka magazynu metryki schematu tabeli](/rest/api/storageservices/storage-analytics-metrics-table-schema).