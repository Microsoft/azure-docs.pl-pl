---
title: Monitorowanie IoT Hub platformy Azure
description: Zacznij tutaj, aby dowiedzieć się, jak monitorować usługę Azure IoT Hub
author: robinsh
ms.author: robinsh
ms.topic: conceptual
ms.service: iot-hub
ms.date: 10/22/2020
ms.openlocfilehash: a8f9c46487422deb4513768dff04f559af952f7b
ms.sourcegitcommit: dd45ae4fc54f8267cda2ddf4a92ccd123464d411
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/29/2020
ms.locfileid: "92926261"
---
# <a name="monitoring-azure-iot-hub"></a>Monitorowanie IoT Hub platformy Azure

Jeśli masz krytyczne aplikacje i procesy biznesowe polegające na zasobach platformy Azure, chcesz monitorować te zasoby pod kątem ich dostępności, wydajności i operacji. W tym artykule opisano dane monitorowania wygenerowane przez usługę Azure IoT Hub i sposób korzystania z funkcji Azure Monitor do analizowania i generowania alertów dotyczących tych danych.

## <a name="monitor-overview"></a>Przegląd monitora

Na stronie **Przegląd** w Azure Portal poszczególnych centrów IoT Hub znajdują się wykresy, które zapewniają pewne metryki użycia, takie jak liczba używanych komunikatów oraz liczba urządzeń podłączonych do centrum IoT Hub.

:::image type="content" source="media/monitor-iot-hub/overview-portal.png" alt-text="Domyślne wykresy metryk na stronie omówienia usługi IoT Hub.":::

Te informacje są przydatne, ale reprezentuje tylko niewielką ilość danych monitorowania, które są dostępne dla Centrum IoT. Niektóre dane monitorowania są zbierane automatycznie i są dostępne do analizy zaraz po utworzeniu Centrum IoT. Dodatkowe typy zbierania danych można włączyć za pomocą jakiejś konfiguracji.

## <a name="what-is-azure-monitor"></a>Co to jest Azure Monitor?

Usługa Azure IoT Hub tworzy dane monitorowania przy użyciu [Azure monitor](/azure/azure-monitor/overview), który jest pełną usługą monitorowania stosu na platformie Azure, która oferuje pełny zestaw funkcji do monitorowania zasobów platformy Azure, a także zasobów w innych chmurach i lokalnych.

Zacznij od artykułu [monitorowanie zasobów platformy Azure za pomocą Azure monitor](/azure/azure-monitor/insights/monitor-azure-resource), który opisuje następujące pojęcia:

- Co to jest Azure Monitor?
- Koszty związane z monitorowaniem
- Monitorowanie danych zebranych na platformie Azure
- Konfigurowanie zbierania danych
- Standardowe narzędzia na platformie Azure na potrzeby analizowania danych monitorowania i powiadamiania o nich

Poniższe sekcje dotyczą tego artykułu, opisując szczegółowe dane zebrane dla platformy Azure IoT Hub i dostarczając przykłady konfigurowania zbierania danych i analizowania tych danych za pomocą narzędzi platformy Azure.

## <a name="monitoring-data"></a>Dane monitorowania

Usługa Azure IoT Hub zbiera te same dane monitorowania co inne zasoby platformy Azure, które są opisane w temacie [monitorowanie danych z zasobów platformy Azure](/azure/azure-monitor/insights/monitor-azure-resource#monitoring-data-from-Azure-resources).

Aby uzyskać szczegółowe informacje na temat metryk i dzienników utworzonych przez usługę Azure IoT Hub, zobacz artykuł [monitorowanie danych usługi azure IoT Hub Data Reference](monitor-iot-hub-reference.md) .

> [!IMPORTANT]
> Zdarzenia emitowane przez usługę IoT Hub przy użyciu Azure Monitor dzienników zasobów nie mają być wiarygodne ani uporządkowane. Niektóre zdarzenia mogą zostać utracone lub dostarczone poza kolejnością. Dzienniki zasobów nie są również przeznaczone do pracy w czasie rzeczywistym i może upłynąć kilka minut, zanim zdarzenia mają być rejestrowane w wybranym miejscu docelowym.

## <a name="collection-and-routing"></a>Kolekcja i Routing

Metryki platformy i dziennik aktywności są zbierane i przechowywane automatycznie, ale mogą być kierowane do innych lokalizacji przy użyciu ustawień diagnostycznych.

Dzienniki zasobów nie są zbierane i przechowywane do momentu utworzenia ustawienia diagnostycznego i skierowania ich do co najmniej jednej lokalizacji.

Metryki i dzienniki można kierować do kilku lokalizacji, w tym:
- Dzienniki Azure Monitor są przechowywane za pośrednictwem skojarzonego Log Analytics obszaru roboczego. Można je analizować przy użyciu Log Analytics.
- Usługa Azure Storage do archiwizacji i analizy w trybie offline 
- Punkt końcowy Event Hubs, gdzie mogą być odczytywane przez aplikacje zewnętrzne, na przykład narzędzia SIEM innych firm.

W Azure Portal można wybrać **Ustawienia diagnostyczne** w obszarze **monitorowanie** w okienku po lewej stronie Centrum IoT, a następnie dodać ustawienia **diagnostyczne** , aby utworzyć ustawienia diagnostyczne w zakresie dzienników i metryk platformy emitowanych przez Centrum IoT.

Poniższy zrzut ekranu przedstawia ustawienie diagnostyczne dla routingu typ dziennika zasobów *operacje połączenia* i wszystkie metryki platformy do obszaru roboczego log Analytics.

:::image type="content" source="media/monitor-iot-hub/diagnostic-setting-portal.png" alt-text="Domyślne wykresy metryk na stronie omówienia usługi IoT Hub.":::

Zobacz [Tworzenie ustawień diagnostycznych, aby zbierać dzienniki platformy i metryki na platformie Azure](/azure/azure-monitor/platform/diagnostic-settings) w celu uzyskania szczegółowego procesu tworzenia ustawień diagnostycznych przy użyciu Azure Portal, interfejsu wiersza polecenia lub programu PowerShell. Podczas tworzenia ustawienia diagnostycznego należy określić, które kategorie dzienników mają być zbierane. Kategorie IoT Hub platformy Azure znajdują się w obszarze [dzienniki zasobów w temacie monitorowanie danych IoT Hub platformy Azure](monitor-iot-hub-reference.md#resource-logs).

Podczas routingu IoT Hub metryki platformy do innych lokalizacji należy pamiętać, że:

- Następujące metryki platformy nie są eksportowane za pośrednictwem ustawień diagnostycznych: *urządzenia połączone (wersja zapoznawcza)* i *łączna liczba urządzeń (wersja zapoznawcza)* .

- Metryki wielowymiarowe, na przykład niektóre [metryki routingu](monitor-iot-hub-reference.md#routing-metrics), są obecnie eksportowane jako spłaszczone metryki jednowymiarowe zagregowane w obrębie wartości wymiarów. Aby uzyskać więcej szczegółów, zobacz [Eksportowanie metryk platformy do innych lokalizacji](/azure/azure-monitor/platform/metrics-supported#exporting-platform-metrics-to-other-locations).

## <a name="analyzing-metrics"></a>Analizowanie metryk

Metryki dla IoT Hub platformy Azure można analizować za pomocą metryk z innych usług platformy Azure przy użyciu Eksploratora metryk, otwierając **metryki** z menu **Azure monitor** . Aby uzyskać szczegółowe informacje na temat korzystania z tego narzędzia, zobacz [Rozpoczynanie pracy z usługą Azure Eksplorator metryk](/azure/azure-monitor/platform/metrics-getting-started) .

W Azure Portal można wybrać **metryki** w obszarze **monitorowanie** w okienku po lewej stronie Centrum IoT, aby otworzyć Eksploratora metryk z zakresem domyślnie do metryk platformy emitowanych przez Centrum IoT Hub:

:::image type="content" source="media/monitor-iot-hub/metrics-portal.png" alt-text="Domyślne wykresy metryk na stronie omówienia usługi IoT Hub.":::

Listę metryk platformy zebranych dla usługi Azure IoT Hub można znaleźć w temacie [Metrics (monitorowanie danych IoT Hub platformy Azure](monitor-iot-hub-reference.md#metrics)). Aby zapoznać się z listą metryk platformy zebranych dla wszystkich usług platformy Azure, zobacz temat [obsługiwane metryki w Azure monitor](/azure/azure-monitor/platform/metrics-supported).

W przypadku metryk IoT Hub platformy, które są zbierane w jednostkach zliczania, niektóre agregacje mogą nie być dostępne ani użyteczne. Aby dowiedzieć się więcej, zobacz temat [obsługiwane agregacje w temacie monitorowanie danych usługi Azure IoT Hub](monitor-iot-hub-reference.md#supported-aggregations).

Niektóre metryki IoT Hub, takie jak [metryki routingu](monitor-iot-hub-reference.md#routing-metrics), są wielowymiarowe. W przypadku tych metryk można zastosować [filtry](/azure-monitor/platform/metrics-charts#apply-filters-to-charts) i [podzielić](/azure/azure-monitor/platform/metrics-charts#apply-splitting-to-a-chart) je na wykresy w oparciu o wymiar.

## <a name="analyzing-logs"></a>Analizowanie dzienników

Dane w dziennikach Azure Monitor są przechowywane w tabelach, w których każda tabela ma swój własny zestaw unikatowych właściwości. Dane w tych tabelach są skojarzone z obszarem roboczym Log Analytics i można je zbadać w Log Analytics. Aby dowiedzieć się więcej na temat dzienników Azure Monitor, zobacz [Omówienie dzienników Azure monitor](/azure/azure-monitor/platform/data-platform-logs) w dokumentacji Azure monitor. 

Aby przekierować dane do dzienników Azure Monitor, należy utworzyć ustawienie diagnostyczne, aby wysyłać dzienniki zasobów lub metryki platformy do Log Analytics obszaru roboczego. Aby dowiedzieć się więcej, zobacz [zbieranie i Routing](#collection-and-routing).

W Azure Portal można wybrać pozycję **dzienniki** w obszarze **monitorowanie** w okienku po lewej stronie Centrum IoT hub, aby przeLog Analytics zapytania o zakres, domyślnie do dzienników i metryk zebranych w Azure monitor dzienników dla Centrum IoT.

:::image type="content" source="media/monitor-iot-hub/logs-portal.png" alt-text="Domyślne wykresy metryk na stronie omówienia usługi IoT Hub.":::

Aby uzyskać listę tabel używanych przez Azure Monitor dzienników i Queryable przez Log Analytics, zobacz [dzienniki dzienników Azure monitor w temacie monitorowanie danych IoT Hub platformy Azure](monitor-iot-hub-reference.md#azure-monitor-logs-tables).

Wszystkie dzienniki zasobów w Azure Monitor mają te same pola, a następnie pola specyficzne dla usługi. Wspólny schemat został przedstawiony w [schemacie dziennika zasobów Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-schema#top-level-resource-logs-schema). Schemat i kategorie dzienników zasobów zbieranych dla IoT Hub platformy Azure można znaleźć w [dziennikach zasobów w temacie monitorowanie danych IoT Hub platformy Azure](monitor-iot-hub-reference.md#resource-logs).

[Dziennik aktywności](/azure/azure-monitor/platform/activity-log) to dziennik platformy na platformie Azure, który zapewnia wgląd w zdarzenia na poziomie subskrypcji. Można wyświetlić go niezależnie lub skierować do dzienników Azure Monitor, w którym można wykonywać dużo bardziej skomplikowane zapytania przy użyciu Log Analytics.  

Podczas routingu IoT Hub metryki platformy do dzienników Azure Monitor należy pamiętać, że:

- Następujące metryki platformy nie są eksportowane za pośrednictwem ustawień diagnostycznych: *urządzenia połączone (wersja zapoznawcza)* i *łączna liczba urządzeń (wersja zapoznawcza)* .

- Metryki wielowymiarowe, na przykład niektóre [metryki routingu](monitor-iot-hub-reference.md#routing-metrics), są obecnie eksportowane jako spłaszczone metryki jednowymiarowe zagregowane w obrębie wartości wymiarów. Aby uzyskać więcej szczegółów, zobacz [Eksportowanie metryk platformy do innych lokalizacji](/azure/azure-monitor/platform/metrics-supported#exporting-platform-metrics-to-other-locations).

W przypadku niektórych typowych zapytań z IoT Hub zobacz [przykładowe zapytania Kusto](#sample-kusto-queries). Aby uzyskać szczegółowe informacje na temat używania zapytań Log Analytics, zobacz [Omówienie zapytań dzienników w programie Azure monitor](/azure/azure-monitor/log-query/log-query-overview).

### <a name="sdk-version-in-iot-hub-logs"></a>Wersja zestawu SDK w dziennikach IoT Hub

Niektóre operacje w dziennikach zasobów IoT Hub zwracają `sdkVersion` Właściwość w `properties` obiekcie. W przypadku tych operacji, gdy aplikacja urządzenia lub zaplecza korzysta z jednego z zestawów SDK usługi Azure IoT, ta właściwość zawiera informacje o używanym zestawie SDK, wersji zestawu SDK i platformie, w której jest uruchomiony zestaw SDK. W poniższym przykładzie przedstawiono `sdkVersion` Właściwość wyemitowaną dla [`deviceConnect`](monitor-iot-hub-reference.md#connections) operacji przy użyciu zestawu SDK urządzeń Node.js: `"azure-iot-device/1.17.1 (node v10.16.0; Windows_NT 10.0.18363; x64)"` . Oto przykład wartości wyemitowanej dla zestawu SDK platformy .NET (C#): `".NET/1.21.2 (.NET Framework 4.8.4200.0; Microsoft Windows 10.0.17763 WindowsProduct:0x00000004; X86)"` .

W poniższej tabeli przedstawiono nazwę zestawu SDK służącą do różnych zestawów SDK usługi Azure IoT:

| Nazwa zestawu SDK we właściwości sdkVersion | Język |
|----------|----------|
| .NET | .NET (C#) |
| Microsoft. Azure. Devices | Zestaw SDK usługi .NET (C#) |
| Microsoft. Azure. Devices. Client | Zestaw SDK urządzeń .NET (C#) |
| usługi iothubclient | C lub Python V1 (przestarzałe) zestaw SDK urządzeń |
| iothubserviceclient | C lub Python V1 (przestarzałe) SDK usługi |
| Azure-IoT-Device-iothub-PR | Zestaw SDK urządzenia w języku Python |
| azure-iot-device | Zestaw SDK urządzeń Node.js |
| azure-iothub | Zestaw SDK usługi Node.js |
| com. Microsoft. Azure. iothub-Java-Client | Zestaw SDK urządzeń Java |
| com. Microsoft. Azure. iothub. Service. Sdk | Zestaw SDK usługi Java |
| com. Microsoft. Azure. Sdk. IoT. IoT-Device-Client | Zestaw SDK urządzeń Java |
| com. Microsoft. Azure. Sdk. IoT. IoT-Service-Client | Zestaw SDK usługi Java |
| C | Embedded C |
| C + (OSSimplified = Azure RTO) | Azure RTOS |

Właściwość wersji zestawu SDK można wyodrębnić podczas wykonywania zapytań dotyczących dzienników zasobów IoT Hub. Na przykład następujące zapytanie wyodrębnia Właściwość wersji zestawu SDK (i identyfikator urządzenia) z właściwości zwracanych przez operacje połączeń. Te dwie właściwości są zapisywane w wynikach wraz z czasem operacji i IDENTYFIKATORem zasobu Centrum IoT, z którym urządzenie nawiązuje połączenie.

```kusto
// SDK version of devices
// List of devices and their SDK versions that connect to IoT Hub
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
| where Category == "Connections"
| extend parsed_json = parse_json(properties_s) 
| extend SDKVersion = tostring(parsed_json.sdkVersion) , DeviceId = tostring(parsed_json.deviceId)
| distinct DeviceId, SDKVersion, TimeGenerated, _ResourceId
```

### <a name="sample-kusto-queries"></a>Przykładowe zapytania Kusto

> [!IMPORTANT]
> Po wybraniu opcji **dzienniki** w menu centrum IoT log Analytics zostanie otwarty z zakresem zapytania ustawionym na bieżące Centrum IoT. Oznacza to, że zapytania dziennika będą zawierać tylko dane z tego zasobu. Jeśli chcesz uruchomić zapytanie, które zawiera dane z innych centrów IoT lub danych z innych usług platformy Azure, wybierz pozycję **dzienniki** z menu **Azure monitor** . Aby uzyskać szczegółowe informacje [, zobacz zakres zapytań dzienników i zakres czasu w Azure Monitor Log Analytics](/azure/azure-monitor/log-query/scope/) .

Poniżej znajdują się zapytania, których można użyć do monitorowania Centrum IoT.

- Błędy łączności: Określ błędy połączenia urządzenia.

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where Category == "Connections" and Level == "Error"
    ```

- Błędy ograniczania: Zidentyfikuj urządzenia, które wystąpiły najwięcej żądań, co spowodowało błędy ograniczające.

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where ResultType == "429001"
    | extend DeviceId = tostring(parse_json(properties_s).deviceId)
    | summarize count() by DeviceId, Category, _ResourceId
    | order by count_ desc
    ```

- Martwe punkty końcowe: Identyfikuj liczbę punktów końcowych martwych lub w złej kondycji według liczby przypadków zgłoszenia problemu, a także przyczyny.

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where Category == "Routes" and OperationName in ("endpointDead", "endpointUnhealthy")
    | extend parsed_json = parse_json(properties_s)
    | extend Endpoint = tostring(parsed_json.endpointName), Reason = tostring(parsed_json.details) 
    | summarize count() by Endpoint, OperationName, Reason, _ResourceId
    | order by count_ desc
    ```

- Podsumowanie błędu: liczba błędów dla wszystkich operacji według typu.

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where Level == "Error"
    | summarize count() by ResultType, ResultDescription, Category, _ResourceId
    ```

- Ostatnio połączone urządzenia: Lista urządzeń, które IoT Hub piły Connect w określonym przedziale czasu.

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where Category == "Connections" and OperationName == "deviceConnect"
    | extend DeviceId = tostring(parse_json(properties_s).deviceId)
    | summarize max(TimeGenerated) by DeviceId, _ResourceId
    ```

- Wersja zestawu SDK urządzeń: Lista urządzeń i ich wersje zestawu SDK dla połączeń urządzeń lub urządzeń z niektórymi operacjami w chmurze.

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where Category == "Connections" or Category == "D2CTwinOperations"
    | extend parsed_json = parse_json(properties_s)
    | extend SDKVersion = tostring(parsed_json.sdkVersion) , DeviceId = tostring(parsed_json.deviceId)
    | distinct DeviceId, SDKVersion, TimeGenerated, _ResourceId
    ```

### <a name="read-logs-from-azure-event-hubs"></a>Odczytuj dzienniki z usługi Azure Event Hubs

Po skonfigurowaniu rejestrowania zdarzeń za pomocą ustawień diagnostycznych można utworzyć aplikacje, które odczytują dzienniki, aby można było wykonać akcję na podstawie zawartych w nich informacji. Ten przykładowy kod pobiera dzienniki z centrum zdarzeń:

```csharp
class Program
{ 
    static string connectionString = "{your AMS eventhub endpoint connection string}";
    static string monitoringEndpointName = "{your AMS event hub endpoint name}";
    static EventHubClient eventHubClient;
    //This is the Diagnostic Settings schema
    class AzureMonitorDiagnosticLog
    {
        string time { get; set; }
        string resourceId { get; set; }
        string operationName { get; set; }
        string category { get; set; }
        string level { get; set; }
        string resultType { get; set; }
        string resultDescription { get; set; }
        string durationMs { get; set; }
        string callerIpAddress { get; set; }
        string correlationId { get; set; }
        string identity { get; set; }
        string location { get; set; }
        Dictionary<string, string> properties { get; set; }
    };

    static void Main(string[] args)
    {
        Console.WriteLine("Monitoring. Press Enter key to exit.\n");
        eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, monitoringEndpointName);
        var d2cPartitions = eventHubClient.GetRuntimeInformationAsync().PartitionIds;
        CancellationTokenSource cts = new CancellationTokenSource();
        var tasks = new List<Task>();
        foreach (string partition in d2cPartitions)
        {
            tasks.Add(ReceiveMessagesFromDeviceAsync(partition, cts.Token));
        }
        Console.ReadLine();
        Console.WriteLine("Exiting...");
        cts.Cancel();
        Task.WaitAll(tasks.ToArray());
    }

    private static async Task ReceiveMessagesFromDeviceAsync(string partition, CancellationToken ct)
    {
        var eventHubReceiver = eventHubClient.GetDefaultConsumerGroup().CreateReceiver(partition, DateTime.UtcNow);
        while (true)
        {
            if (ct.IsCancellationRequested)
            {
                await eventHubReceiver.CloseAsync();
                break;
            }
            EventData eventData = await eventHubReceiver.ReceiveAsync(new TimeSpan(0,0,10));
            if (eventData != null)
            {
                string data = Encoding.UTF8.GetString(eventData.GetBytes());
                Console.WriteLine("Message received. Partition: {0} Data: '{1}'", partition, data);
                var deserializer = new JavaScriptSerializer();
                //deserialize json data to azure monitor object
                AzureMonitorDiagnosticLog message = new JavaScriptSerializer().Deserialize<AzureMonitorDiagnosticLog>(result);
            }
        }
    }
}
```

## <a name="alerts"></a>Alerty

Alerty Azure Monitor z wyprzedzeniem powiadamiają Cię, gdy w danych monitorowania zostaną znalezione ważne warunki. Umożliwiają identyfikowanie i rozwiązywanie problemów w systemie przed ich zapisaniem przez klientów. Można ustawić alerty dotyczące [metryk](/azure/azure-monitor/platform/alerts-metric-overview), [dzienników](/azure/azure-monitor/platform/alerts-unified-log)i [dziennika aktywności](/azure/azure-monitor/platform/activity-log-alerts). Różne typy alertów mają zalety i wady.

Podczas tworzenia reguły alertu na podstawie metryk platformy należy pamiętać, że w przypadku metryk IoT Hub platformy, które są zbierane w jednostkach zliczania, niektóre agregacje mogą nie być dostępne ani użyteczne. Aby dowiedzieć się więcej, zobacz temat [obsługiwane agregacje w temacie monitorowanie danych usługi Azure IoT Hub](monitor-iot-hub-reference.md#supported-aggregations).

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać informacje na temat metryk, dzienników i innych ważnych wartości utworzonych przez program [nazwa usługi], zobacz artykuł [monitorowanie danych usługi Azure IoT Hub](monitor-iot-hub-reference.md) .

- Aby uzyskać szczegółowe informacje na temat monitorowania zasobów platformy Azure, zobacz [monitorowanie zasobów platformy Azure za pomocą Azure monitor](/azure/azure-monitor/insights/monitor-azure-resource) .
