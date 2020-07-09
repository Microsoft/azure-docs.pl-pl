---
title: Monitoruj kondycję IoT Hub platformy Azure | Microsoft Docs
description: Używanie Azure Monitor i Azure Resource Health do monitorowania IoT Hub i szybkiego diagnozowania problemów
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: robinsh
ms.custom: amqp
ms.openlocfilehash: d00e3dc5e43eb6978f6835ac4b7d101e4a42a226
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84792046"
---
# <a name="monitor-the-health-of-azure-iot-hub-and-diagnose-problems-quickly"></a>Monitorowanie kondycji usługi Azure IoT Hub i szybkie diagnozowanie problemów

Firmy, które implementują platformę Azure IoT Hub, oczekują niezawodnej wydajności z zasobów. Aby ułatwić zachowanie ścisłej czujki na potrzeby operacji, IoT Hub jest w pełni zintegrowana z [Azure monitor](../azure-monitor/index.yml) i [Azure Resource Health](../service-health/resource-health-overview.md). Te dwie usługi współpracują z danymi, które są potrzebne w celu zapewnienia prawidłowego działania rozwiązań IoT.

Azure Monitor to pojedyncze Źródło monitorowania i rejestrowania dla wszystkich usług platformy Azure. Można wysłać dzienniki diagnostyczne, które Azure Monitor generuje do Azure Monitor dzienników, Event Hubs lub Azure Storage na potrzeby przetwarzania niestandardowego. Metryki i ustawienia diagnostyki Azure Monitor zapewniają wgląd w wydajność zasobów. Przejdź do tego artykułu, aby dowiedzieć się, jak [korzystać z Azure monitor](#use-azure-monitor) w usłudze IoT Hub. 

> [!IMPORTANT]
> Zdarzenia emitowane przez usługę IoT Hub przy użyciu dzienników diagnostycznych Azure Monitor nie są gwarantowane lub uporządkowane. Niektóre zdarzenia mogą zostać utracone lub dostarczone poza kolejnością. Dzienniki diagnostyczne nie są również przeznaczone do pracy w czasie rzeczywistym i może upłynąć kilka minut, zanim zdarzenia mają być rejestrowane w wybranym miejscu docelowym.

Azure Resource Health pomaga zdiagnozować i uzyskać pomoc techniczną, gdy problem z platformą Azure ma wpływ na zasoby. Pulpit nawigacyjny zawiera bieżący i poprzedni stan kondycji dla każdego z centrów IoT. Przejdź do sekcji w dolnej części tego artykułu, aby dowiedzieć się, jak [korzystać z Azure Resource Health](#use-azure-resource-health) w usłudze IoT Hub. 

IoT Hub również zawiera własne metryki, których można użyć do zrozumienia stanu zasobów IoT. Aby dowiedzieć się więcej, zobacz [Opis metryk IoT Hub](iot-hub-metrics.md).

## <a name="use-azure-monitor"></a>Korzystanie z usługi Azure Monitor

Azure Monitor udostępnia informacje diagnostyczne dotyczące zasobów platformy Azure, co oznacza, że można monitorować operacje, które mają miejsce w usłudze IoT Hub.

Aby dowiedzieć się więcej o określonych metrykach i zdarzeniach, które Azure Monitor zegarki, zobacz temat [obsługiwane metryki z Azure monitor](../azure-monitor/platform/metrics-supported.md) i [obsługiwanymi usługami, schematami i kategoriami dla dzienników diagnostycznych platformy Azure](../azure-monitor/platform/diagnostic-logs-schema.md).

[!INCLUDE [iot-hub-diagnostics-settings](../../includes/iot-hub-diagnostics-settings.md)]

### <a name="understand-the-logs"></a>Objaśnienie dzienników

Azure Monitor śledzi różne operacje wykonywane w IoT Hub. Każda kategoria zawiera schemat, który definiuje sposób zgłaszania zdarzeń w tej kategorii.

#### <a name="connections"></a>Połączenia

Kategoria połączenia śledzi zdarzenia łączenia i rozłączania urządzeń z Centrum IoT Hub, a także błędy. Ta kategoria jest przydatna do identyfikowania nieautoryzowanych prób połączeń i alertów w przypadku utraty połączenia z urządzeniami.

> [!NOTE]
> W przypadku stanu niezawodnego połączenia urządzenia sprawdź [puls urządzenia](iot-hub-devguide-identity-registry.md#device-heartbeat).

```json
{
   "records":
   [
        {
            "time": " UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "deviceConnect",
            "category": "Connections",
            "level": "Information",
            "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"maskedIpAddress\":\"<maskedIpAddress>\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="cloud-to-device-commands"></a>Polecenia z chmury do urządzenia

Kategoria poleceń z chmury do urządzenia śledzi błędy występujące w centrum IoT Hub i powiązane z potokiem komunikatów z chmury do urządzenia. Ta kategoria zawiera błędy występujące w programie:

* Wysyłanie komunikatów z chmury do urządzeń (takich jak błędy nieautoryzowanego nadawcy),
* Otrzymywanie komunikatów z chmury do urządzeń (takich jak liczba błędów przekraczania liczby dostaw) i
* Otrzymywanie informacji zwrotnych z chmury do urządzenia (na przykład błędów ważności opinii).

Ta kategoria nie przechwytuje błędów, gdy komunikat z chmury do urządzenia jest dostarczany pomyślnie, ale nieprawidłowo obsłużony przez urządzenie.

```json
{
    "records":
    [
        {
            "time": " UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "messageExpired",
            "category": "C2DCommands",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"deviceId\":\"<deviceId>\",\"messageId\":\"<messageId>\",\"messageSizeInBytes\":\"<messageSize>\",\"protocol\":\"Amqp\",\"deliveryAcknowledgement\":\"<None, NegativeOnly, PositiveOnly, Full>\",\"deliveryCount\":\"0\",\"expiryTime\":\"<timestamp>\",\"timeInSystem\":\"<timeInSystem>\",\"ttl\":<ttl>, \"EventProcessedUtcTime\":\"<UTC timestamp>\",\"EventEnqueuedUtcTime\":\"<UTC timestamp>\", \"maskedIpAddress\": \"<maskedIpAddress>\", \"statusCode\": \"4XX\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="device-identity-operations"></a>Operacje tożsamości urządzeń

Kategoria operacje tożsamości urządzenia śledzi błędy występujące podczas próby utworzenia, zaktualizowania lub usunięcia wpisu w rejestrze tożsamości Centrum IoT. Śledzenie tej kategorii jest przydatne w scenariuszach aprowizacji.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "get",
            "category": "DeviceIdentityOperations",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"maskedIpAddress\":\"<maskedIpAddress>\",\"deviceId\":\"<deviceId>\", \"statusCode\":\"4XX\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="routes"></a>Trasy

Kategoria [routingu wiadomości](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c) śledzi błędy występujące podczas oceny trasy komunikatów i kondycji punktu końcowego, jak zostało to postrzegane przez IoT Hub. Ta kategoria obejmuje następujące zdarzenia:

* Reguła ma wartość "undefined".
* IoT Hub oznacza punkt końcowy jako martwy lub
* Wszystkie błędy odebrane z punktu końcowego.

Ta kategoria nie zawiera określonych błędów dotyczących samych komunikatów (takich jak błędy ograniczania urządzeń), które są raportowane w kategorii "dane telemetryczne urządzenia".

```json
{
    "records":
    [
        {
            "time":"2019-12-12T03:25:14Z",
            "resourceId":"/SUBSCRIPTIONS/91R34780-3DEC-123A-BE2A-213B5500DFF0/RESOURCEGROUPS/ANON-TEST/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/ANONHUB1",
            "operationName":"endpointUnhealthy",
            "category":"Routes",
            "level":"Error",
            "resultType":"403004",
            "resultDescription":"DeviceMaximumQueueDepthExceeded",
            "properties":"{\"deviceId\":null,\"endpointName\":\"anon-sb-1\",\"messageId\":null,\"details\":\"DeviceMaximumQueueDepthExceeded\",\"routeName\":null,\"statusCode\":\"403\"}",
            "location":"westus"
        }
    ]
}
```

Poniżej przedstawiono więcej szczegółów dotyczących routingu dzienników diagnostycznych:

* [Lista kodów błędów dzienników diagnostycznych routingu](troubleshoot-message-routing.md#diagnostics-error-codes)
* [Lista operationNames dzienników diagnostycznych routingu](troubleshoot-message-routing.md#diagnostics-operation-names)

#### <a name="device-telemetry"></a>Dane telemetryczne urządzenia

Kategoria telemetrii urządzenia śledzi błędy występujące w centrum IoT i są powiązane z potokiem telemetrii. Ta kategoria zawiera błędy występujące podczas wysyłania zdarzeń telemetrii (takich jak ograniczenie przepustowości) i otrzymywanie zdarzeń telemetrii (na przykład nieautoryzowanego czytnika). Ta kategoria nie może przechwytywać błędów spowodowanych przez kod uruchomiony na urządzeniu.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "ingress",
            "category": "DeviceTelemetry",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"deviceId\":\"<deviceId>\",\"batching\":\"0\",\"messageSizeInBytes\":\"<messageSizeInBytes>\",\"EventProcessedUtcTime\":\"<UTC timestamp>\",\"EventEnqueuedUtcTime\":\"<UTC timestamp>\",\"partitionId\":\"1\"}", 
            "location": "Resource location"
        }
    ]
}
```

#### <a name="file-upload-operations"></a>Operacje przekazywania plików

Kategoria przekazywanie plików śledzi błędy występujące w centrum IoT Hub i są powiązane z funkcją przekazywania plików. Ta kategoria obejmuje:

* Błędy występujące przy użyciu identyfikatora URI sygnatury dostępu współdzielonego, na przykład kiedy wygasa przed powiadomieniem centrum o ukończonym przekazywaniu przez urządzenie.

* Przekazywanie zgłoszone przez urządzenie nie powiodło się.

* Błędy występujące, gdy podczas tworzenia komunikatu powiadomienia IoT Hub nie znaleziono pliku w magazynie.

Ta kategoria nie może przechwytywać błędów, które są bezpośrednio wykonywane, gdy urządzenie przekazuje plik do magazynu.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "ingress",
            "category": "FileUploadOperations",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"blobUri\":\"http//bloburi.com\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="cloud-to-device-twin-operations"></a>Operacje na sznurze z chmury do urządzenia

Kategoria operacji dla sznurów z chmury do urządzenia śledzi zdarzenia zainicjowane przez usługę na urządzeniu bliźniaczych reprezentacji. Operacje te mogą obejmować pobieranie, aktualizowanie i zastępowanie tagów oraz aktualizowanie lub zastępowanie żądanych właściwości.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "read",
            "category": "C2DTwinOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"sdkVersion\":\"<sdkVersion>\",\"messageSize\":\"<messageSize>\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="device-to-cloud-twin-operations"></a>Operacje wieloosiowe między urządzeniami a chmurą

Kategoria operacji ze osiową między urządzeniami a chmurą śledzi zdarzenia zainicjowane przez urządzenie w usłudze Device bliźniaczych reprezentacji. Te operacje mogą obejmować pobieranie sznurka, aktualizowanie raportowanych właściwości i subskrybowanie żądanych właściwości.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "update",
            "category": "D2CTwinOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authenticationType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="twin-queries"></a>Zapytania bliźniaczye

Kategoria zapytania dwuosiowe raportuje żądania zapytań dotyczące bliźniaczych reprezentacji urządzeń, które są inicjowane w chmurze.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "query",
            "category": "TwinQueries",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"query\":\"<twin query>\",\"sdkVersion\":\"<sdkVersion>\",\"messageSize\":\"<messageSize>\",\"pageSize\":\"<pageSize>\", \"continuation\":\"<true, false>\", \"resultSize\":\"<resultSize>\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="jobs-operations"></a>Operacje zadań

Kategoria operacje zadań raportuje żądania zadań, aby zaktualizować bliźniaczych reprezentacji urządzeń lub wywołać metody bezpośrednie na wielu urządzeniach. Te żądania są inicjowane w chmurze.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "jobCompleted",
            "category": "JobsOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"jobId\":\"<jobId>\", \"sdkVersion\": \"<sdkVersion>\",\"messageSize\": <messageSize>,\"filter\":\"DeviceId IN ['1414ded9-b445-414d-89b9-e48e8c6285d5']\",\"startTimeUtc\":\"Wednesday, September 13, 2017\",\"duration\":\"0\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="direct-methods"></a>Metody bezpośrednie

Kategoria metody bezpośrednie śledzi interakcje z żądaniem odpowiedzi wysyłane do poszczególnych urządzeń. Te żądania są inicjowane w chmurze.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "send",
            "category": "DirectMethods",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":<messageSize>, \"RequestSize\": 1, \"ResponseSize\": 1, \"sdkVersion\": \"2017-07-11\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="distributed-tracing-preview"></a>Śledzenie rozproszone (wersja zapoznawcza)

Kategoria śledzenie rozproszone śledzi identyfikatory korelacji dla komunikatów, które zawierają nagłówek kontekstu śledzenia. Aby w pełni włączyć te dzienniki, kod po stronie klienta należy zaktualizować, wykonując następujące czynności: [analizowanie i diagnozowanie aplikacji IoT w ramach kompleksowego śledzenia IoT Hub (wersja zapoznawcza)](iot-hub-distributed-tracing.md).

Należy pamiętać, że jest to zgodne z `correlationId` propozycją w [kontekście śledzenia W3C](https://github.com/w3c/trace-context) , w której znajduje się, a także `trace-id` `span-id` .

##### <a name="iot-hub-d2c-device-to-cloud-logs"></a>Dzienniki IoT Hub D2C (urządzenie-chmura)

IoT Hub rejestruje ten dziennik, gdy komunikat zawierający prawidłowe właściwości śledzenia dociera do IoT Hub.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubD2C",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-0144d2590aacd909-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Receive message success",
            "durationMs": "",
            "properties": "{\"messageSize\": 1, \"deviceId\":\"<deviceId>\", \"callerLocalTimeUtc\": : \"2017-02-22T03:27:28.633Z\", \"calleeLocalTimeUtc\": \"2017-02-22T03:27:28.687Z\"}",
            "location": "Resource location"
        }
    ]
}
```

W tym miejscu `durationMs` nie jest obliczany, ponieważ zegar IoT Hub może nie być zsynchronizowany z zegarem urządzenia i w ten sposób Obliczanie czasu trwania może być mylące. Zalecamy zapisanie logiki przy użyciu sygnatur czasowych w `properties` sekcji, aby przechwytywać opóźnienia w czasie oczekiwania między urządzeniami a chmurą.

| Właściwość | Typ | Opis |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **messageSize** | Integer | Rozmiar komunikatu z urządzenia do chmury w bajtach |
| **deviceId** | Ciąg znaków alfanumerycznych ASCII 7-bitowych | Tożsamość urządzenia |
| **callerLocalTimeUtc** | Sygnatura czasowa UTC | Godzina utworzenia komunikatu zgłoszonego przez zegar lokalny urządzenia |
| **calleeLocalTimeUtc** | Sygnatura czasowa UTC | Godzina przybycia wiadomości w bramie IoT Hubej zgłoszonej przez IoT Hub zegar po stronie usługi |

##### <a name="iot-hub-ingress-logs"></a>IoT Hub dzienniki danych wejściowych

IoT Hub rejestruje ten dziennik, gdy komunikat zawierający prawidłowe właściwości śledzenia jest zapisywany w wewnętrznym lub wbudowanym centrum zdarzeń.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubIngress",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-349810a9bbd28730-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Ingress message success",
            "durationMs": "10",
            "properties": "{\"isRoutingEnabled\": \"true\", \"parentSpanId\":\"0144d2590aacd909\"}",
            "location": "Resource location"
        }
    ]
}
```

W `properties` sekcji ten dziennik zawiera dodatkowe informacje na temat przychodzących komunikatów.

| Właściwość | Typ | Opis |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **isRoutingEnabled** | String | Wartość true lub false wskazuje, czy w IoT Hub jest włączona funkcja routingu komunikatów |
| **parentSpanId** | String | [Identyfikator zakresu](https://w3c.github.io/trace-context/#parent-id) komunikatu nadrzędnego, który będzie w tym przypadku D2C śledzenia komunikatów |

##### <a name="iot-hub-egress-logs"></a>Dzienniki wychodzące IoT Hub

IoT Hub rejestruje ten dziennik, gdy [Routing](iot-hub-devguide-messages-d2c.md) jest włączony, a komunikat jest zapisywana w [punkcie końcowym](iot-hub-devguide-endpoints.md). Jeśli Routing nie jest włączony, IoT Hub nie rejestruje tego dziennika.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubEgress",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-98ac3578922acd26-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Egress message success",
            "durationMs": "10",
            "properties": "{\"endpointType\": \"EventHub\", \"endpointName\": \"myEventHub\", \"parentSpanId\":\"349810a9bbd28730\"}",
            "location": "Resource location"
        }
    ]
}
```

W `properties` sekcji ten dziennik zawiera dodatkowe informacje na temat przychodzących komunikatów.

| Właściwość | Typ | Opis |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **Nazwapunktukoncowego** | String | Nazwa punktu końcowego routingu |
| **punkt końcowy** | String | Typ punktu końcowego routingu |
| **parentSpanId** | String | [Identyfikator zakresu](https://w3c.github.io/trace-context/#parent-id) komunikatu nadrzędnego, który będzie IoT Hub śledzenia komunikatów przychodzących w tym przypadku |

#### <a name="configurations"></a>Konfiguracje

Dzienniki konfiguracji IoT Hub śledzą zdarzenia i błędy dla zestawu funkcji automatycznego zarządzania urządzeniami.

```json
{
    "records":
    [
         {
             "time": "2019-09-24T17:21:52Z",
             "resourceId": "Resource Id",
             "operationName": "ReadManyConfigurations",
             "category": "Configurations",
             "resultType": "",
             "resultDescription": "",
             "level": "Information",
             "durationMs": "17",
             "properties": "{\"configurationId\":\"\",\"sdkVersion\":\"2018-06-30\",\"messageSize\":\"0\",\"statusCode\":null}",
             "location": "southcentralus"
         }
    ]
}
```

### <a name="device-streams-preview"></a>Strumienie urządzeń (wersja zapoznawcza)

Kategoria strumienie urządzenia śledzi interakcje z żądaniem odpowiedzi wysyłane do poszczególnych urządzeń.

```json
{
    "records":
    [
         {
             "time": "2019-09-19T11:12:04Z",
             "resourceId": "Resource Id",
             "operationName": "invoke",
             "category": "DeviceStreams",
             "resultType": "",
             "resultDescription": "",    
             "level": "Information",
             "durationMs": "74",
             "properties": "{\"deviceId\":\"myDevice\",\"moduleId\":\"myModule\",\"sdkVersion\":\"2019-05-01-preview\",\"requestSize\":\"3\",\"responseSize\":\"5\",\"statusCode\":null,\"requestName\":\"myRequest\",\"direction\":\"c2d\"}",
             "location": "Central US"
         }
    ]
}
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

## <a name="use-azure-resource-health"></a>Użyj Azure Resource Health

Użyj Azure Resource Health, aby monitorować, czy Centrum IoT jest uruchomione. Można również dowiedzieć się, czy regionalna awaria ma wpływ na kondycję Centrum IoT. Aby poznać szczegółowe informacje o stanie kondycji IoT Hub platformy Azure, zalecamy [użycie Azure monitor](#use-azure-monitor).

Usługa Azure IoT Hub wskazuje na poziom regionalny kondycję. Jeśli regionalna awaria ma wpływ na centrum IoT Hub, stan kondycji jest wyświetlany jako **nieznany**. Aby dowiedzieć się więcej, zobacz [typy zasobów i kontrole kondycji w usłudze Azure Resource Health](../service-health/resource-health-checks-resource-types.md).

Aby sprawdzić kondycję centrów IoT, wykonaj następujące kroki:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Przejdź do **Service Health**  >  **kondycji zasobów**.

3. Z listy rozwijanej wybierz subskrypcję, a następnie wybierz **IoT Hub** jako typ zasobu.

Aby dowiedzieć się więcej na temat sposobu interpretacji danych dotyczących kondycji, zobacz [Omówienie usługi Azure Resource Health](../service-health/resource-health-overview.md).

## <a name="next-steps"></a>Następne kroki

* [Informacje o metrykach IoT Hub](iot-hub-metrics.md)
* [Zdalne monitorowanie i powiadomienia w usłudze IoT przy użyciu Azure Logic Apps łączenia Centrum IoT i skrzynki pocztowej](iot-hub-monitoring-notifications-with-azure-logic-apps.md)
