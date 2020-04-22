---
title: Monitorowanie kondycji usługi Azure IoT Hub | Dokumenty firmy Microsoft
description: Użyj usługi Azure Monitor i usługi Azure Resource Health, aby monitorować centrum IoT Hub i szybko diagnozować problemy
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: kgremban
ms.custom: amqp
ms.openlocfilehash: a1d74085090a3e20764d7b6fee84ffca52d5cb74
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732427"
---
# <a name="monitor-the-health-of-azure-iot-hub-and-diagnose-problems-quickly"></a>Monitorowanie kondycji usługi Azure IoT Hub i szybkie diagnozowanie problemów

Firmy, które implementują usługę Azure IoT Hub, oczekują niezawodnej wydajności ze swoich zasobów. Aby ułatwić ci obserwowanie operacji, usługa IoT Hub jest w pełni zintegrowana z [usługą Azure Monitor](../azure-monitor/index.yml) i [usługą Azure Resource Health.](../service-health/resource-health-overview.md) Te dwie usługi działają w celu zapewnienia danych potrzebnych do utrzymania rozwiązań IoT w dobrej kondycji.

Usługa Azure Monitor jest jednym źródłem monitorowania i rejestrowania wszystkich usług platformy Azure. Dzienniki diagnostyczne generowane przez usługę Azure Monitor można wysyłać do dzienników usługi Azure Monitor, centrów zdarzeń lub usługi Azure Storage w celu przetwarzania niestandardowego. Ustawienia metryk i diagnostyki usługi Azure Monitor zapewniają wgląd w wydajność zasobów. Kontynuuj czytanie tego artykułu, aby dowiedzieć się, jak [korzystać z usługi Azure Monitor](#use-azure-monitor) z centrum IoT Hub. 

> [!IMPORTANT]
> Zdarzenia emitowane przez usługę Usługi IoT Hub przy użyciu dzienników diagnostycznych usługi Azure Monitor nie są gwarantowane jako niezawodne lub uporządkowane. Niektóre zdarzenia mogą zostać utracone lub dostarczone poza kolejnością. Dzienniki diagnostyczne również nie są przeznaczone do czasu rzeczywistego i może upłynąć kilka minut, aby zdarzenia były rejestrowane do wybranego miejsca docelowego.

Usługa Azure Resource Health pomaga diagnozować i utrzymywania pomocy technicznej, gdy problem z platformą Azure wpływa na zasoby. Pulpit nawigacyjny zapewnia bieżący i przeszły stan kondycji dla każdego z centrów IoT. Przejdź do sekcji u dołu tego artykułu, aby dowiedzieć się, jak [korzystać z usługi Azure Resource Health](#use-azure-resource-health) z centrum IoT hub. 

Centrum IoT hub udostępnia również własne metryki, których można użyć do zrozumienia stanu zasobów IoT. Aby dowiedzieć się więcej, zobacz [Opis metryk usługi IoT Hub](iot-hub-metrics.md).

## <a name="use-azure-monitor"></a>Korzystanie z usługi Azure Monitor

Usługa Azure Monitor udostępnia informacje diagnostyczne dotyczące zasobów platformy Azure, co oznacza, że można monitorować operacje, które mają miejsce w centrum IoT Hub.

Ustawienia diagnostyczne usługi Azure Monitor zastępują monitor operacyjny usługi IoT Hub. Jeśli obecnie używasz monitorowania operacji, należy przeprowadzić migrację przepływów pracy. Aby uzyskać więcej informacji, zobacz [Migrowanie z monitorowania operacji do ustawień diagnostycznych](iot-hub-migrate-to-diagnostics-settings.md).

Aby dowiedzieć się więcej o określonych metrykach i zdarzeniach, które obserwuje usługa Azure Monitor, zobacz [Obsługiwane metryki za pomocą usługi Azure Monitor](../azure-monitor/platform/metrics-supported.md) i [obsługiwanych usług, schematów i kategorii dla dzienników diagnostycznych platformy Azure.](../azure-monitor/platform/diagnostic-logs-schema.md)

[!INCLUDE [iot-hub-diagnostics-settings](../../includes/iot-hub-diagnostics-settings.md)]

### <a name="understand-the-logs"></a>Objaśnienie dzienników

Usługa Azure Monitor śledzi różne operacje, które występują w centrum IoT Hub. Każda kategoria ma schemat, który definiuje sposób zgłaszania zdarzeń w tej kategorii.

#### <a name="connections"></a>Połączenia

Kategoria połączeń śledzi, jak urządzenia łączą się i rozłączają zdarzenia od centrum IoT hub, a także błędy. Ta kategoria jest przydatna do identyfikowania nieautoryzowanych prób połączenia i alertów o utracie połączenia z urządzeniami.

> [!NOTE]
> Aby uzyskać wiarygodny stan połączenia urządzeń, sprawdź [pulsu urządzenia](iot-hub-devguide-identity-registry.md#device-heartbeat).

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

#### <a name="cloud-to-device-commands"></a>Polecenia chmury do urządzenia

Kategoria poleceń chmury do urządzenia śledzi błędy występujące w centrum IoT hub i są związane z potokiem komunikatów z chmury do urządzenia. Ta kategoria zawiera błędy, które występują z:

* Wysyłanie komunikatów z chmury do urządzenia (takich jak nieautoryzowane błędy nadawcy),
* Odbieranie komunikatów z chmury do urządzenia (np. liczba
* Odbieranie opinii o wiadomościach z chmury do urządzenia (takich jak błędy wygasłe opinie).

Ta kategoria nie wychwytuje błędy, gdy komunikat z chmury do urządzenia jest dostarczany pomyślnie, ale następnie nieprawidłowo obsługiwane przez urządzenie.

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

#### <a name="device-identity-operations"></a>Operacje tożsamości urządzenia

Kategoria operacji tożsamości urządzenia śledzi błędy, które występują podczas próby utworzenia, aktualizacji lub usunięcia wpisu w rejestrze tożsamości centrum IoT. Śledzenie tej kategorii jest przydatne w przypadku scenariuszy inicjowania obsługi administracyjnej.

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

Kategoria routingu wiadomości śledzi błędy występujące podczas oceny trasy wiadomości i kondycji punktu końcowego jako postrzegane przez Centrum IoT Hub. Ta kategoria obejmuje zdarzenia, takie jak:

* Reguła jest oceniana jako "niezdefiniowana",
* Centrum IoT oznacza punkt końcowy jako martwy, lub
* Wszelkie błędy odebrane z punktu końcowego. 

Ta kategoria nie zawiera określonych błędów dotyczących samych komunikatów (takich jak błędy ograniczania przepustowości urządzenia), które są zgłaszane w kategorii "telemetria urządzenia".

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "endpointUnhealthy",
            "category": "Routes",
            "level": "Error",
            "properties": "{\"deviceId\": \"<deviceId>\",\"endpointName\":\"<endpointName>\",\"messageId\":<messageId>,\"details\":\"<errorDetails>\",\"routeName\": \"<routeName>\"}",
            "location": "Resource location"
        }
    ]
}
```

#### <a name="device-telemetry"></a>Telemetria urządzenia

Kategoria telemetrii urządzenia śledzi błędy występujące w centrum IoT hub i są związane z potoku telemetrii. Ta kategoria zawiera błędy, które występują podczas wysyłania zdarzeń telemetrycznych (takich jak ograniczanie przepustowości) i odbieranie zdarzeń telemetrycznych (takich jak nieautoryzowany czytnik). Ta kategoria nie może przechwytyć błędów spowodowanych przez kod uruchomiony na samym urządzeniu.

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

Kategoria przekazywania plików śledzi błędy występujące w centrum IoT i są związane z funkcją przekazywania plików. Kategoria ta obejmuje:

* Błędy występujące w identyfikatorze URI sygnatury dostępu Współdzielonego, na przykład po wygaśnięciu, zanim urządzenie powiadomi centrum zakończonego przekazywania.

* Nie powiodło się przesłania zgłoszone przez urządzenie.

* Błędy, które występują, gdy plik nie zostanie znaleziony w magazynie podczas tworzenia komunikatów powiadomień Usługi IoT Hub.

Ta kategoria nie może przechwytywać błędów, które występują bezpośrednio podczas przekazywania pliku do magazynu.

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

#### <a name="cloud-to-device-twin-operations"></a>Operacje bliźniaczeczki chmury na urządzenia

Kategoria operacji bliźniaczych reprezentacji chmury do urządzenia śledzi zdarzenia inicjowane usługi na bliźniaczych reprezentacji urządzeń. Operacje te mogą obejmować uzyskać bliźniaczej reprezentacji, aktualizacji lub zastąpić tagi i zaktualizować lub zastąpić żądane właściwości.

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

#### <a name="device-to-cloud-twin-operations"></a>Operacje bliźniaczych między urządzeniami do chmury

Kategoria operacji bliźniaczych reprezentacji urządzenia do chmury śledzi zdarzenia inicjowane przez urządzenie na bliźniaczych reprezentacjach urządzeń. Operacje te mogą obejmować uzyskać bliźniaczej reprezentacji, zaktualizować zgłaszane właściwości i subskrybować żądane właściwości.

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

#### <a name="twin-queries"></a>Zapytania bliźniacze

Kategoria zapytań bliźniaczych reprezentacji raportuje żądania zapytań dotyczących bliźniaczych reprezentacji urządzeń, które są inicjowane w chmurze.

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

Kategoria operacji zadań raportuje żądania zadania, aby zaktualizować bliźniacze dane urządzenia lub wywołać metody bezpośrednie na wielu urządzeniach. Te żądania są inicjowane w chmurze.

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

Kategoria metody bezpośrednie śledzi interakcje żądanie-odpowiedź wysyłane do poszczególnych urządzeń. Te żądania są inicjowane w chmurze.

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

Rozproszona kategoria śledzenia śledzi identyfikatory korelacji dla wiadomości, które zawierają nagłówek kontekstu śledzenia. Aby w pełni włączyć te dzienniki, kod po stronie klienta musi zostać zaktualizowany, wykonując [analizę i diagnozowanie aplikacji IoT end-to-end za pomocą śledzenia rozproszonego (podgląd) W centrum IoT Hub.](iot-hub-distributed-tracing.md)

Należy `correlationId` zauważyć, że jest zgodny z propozycją [kontekstu śledzenia W3C,](https://github.com/w3c/trace-context) gdzie zawiera as, `trace-id` jak i `span-id`.

##### <a name="iot-hub-d2c-device-to-cloud-logs"></a>Dzienniki IoT Hub D2C (device-to-cloud)

Usługa IoT Hub rejestruje ten dziennik, gdy wiadomość zawierająca prawidłowe właściwości śledzenia dociera do usługi IoT Hub.

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

W `durationMs` tym miejscu nie jest obliczana jako zegar usługi IoT Hub może nie być zsynchronizowany z zegarem urządzenia, a zatem obliczanie czasu trwania może być mylące. Zaleca się pisanie logiki przy użyciu `properties` sygnatur czasowych w sekcji do przechwytywania skoków opóźnienia urządzenia do chmury.

| Właściwość | Typ | Opis |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **messageSize (rozmiar wiadomości)** | Liczba całkowita | Rozmiar komunikatu między urządzeniami a chmurą w bajtach |
| **deviceId** | Ciąg 7-bitowych znaków alfanumerycznej ASCII | Tożsamość urządzenia |
| **callerLocalTimeUtc** | Sygnatura czasowa UTC | Czas utworzenia wiadomości zgłoszony przez zegar lokalny urządzenia |
| **calleeLocalTimeUtc** | Sygnatura czasowa UTC | Czas nadejścia wiadomości do bramy usługi IoT Hub zgłoszony przez zegar po stronie usługi Usługi IoT Hub |

##### <a name="iot-hub-ingress-logs"></a>Dzienniki transferu ruchu przychodzącego usługi IoT Hub

Usługa IoT Hub rejestruje ten dziennik, gdy komunikat zawierający prawidłowe właściwości śledzenia zapisuje do centrum zdarzeń wewnętrznych lub wbudowanych.

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

W `properties` tej sekcji ten dziennik zawiera dodatkowe informacje o danych przychodzących wiadomości.

| Właściwość | Typ | Opis |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **isRoutingEnabled (Nie można pochwalać)** | Ciąg | Prawda lub fałsz, wskazuje, czy routing wiadomości jest włączony w Centrum IoT Hub |
| **parentSpanId** | Ciąg | [Span-id](https://w3c.github.io/trace-context/#parent-id) wiadomości nadrzędnej, która byłaby śledzenia wiadomości D2C w tym przypadku |

##### <a name="iot-hub-egress-logs"></a>Dzienniki wychodzące usługi IoT Hub

Usługa IoT Hub rejestruje ten dziennik, gdy [routing](iot-hub-devguide-messages-d2c.md) jest włączony, a wiadomość jest zapisywana w [punkcie końcowym.](iot-hub-devguide-endpoints.md) Jeśli routing nie jest włączony, usługa IoT Hub nie rejestruje tego dziennika.

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

W `properties` tej sekcji ten dziennik zawiera dodatkowe informacje o danych przychodzących wiadomości.

| Właściwość | Typ | Opis |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **nazwa punktu końcowego** | Ciąg | Nazwa punktu końcowego marszruty |
| **Endpointtype** | Ciąg | Typ punktu końcowego marszruty |
| **parentSpanId** | Ciąg | [Span-id](https://w3c.github.io/trace-context/#parent-id) wiadomości nadrzędnej, która byłaby śledzenia komunikatu przychodzącego Usługi IoT Hub w tym przypadku |

#### <a name="configurations"></a>Konfiguracje

Dzienniki konfiguracji usługi IoT Hub śledzą zdarzenia i błędy zestawu funkcji automatycznego zarządzania urządzeniami.

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

Kategoria strumieni urządzenia śledzi interakcje żądanie-odpowiedź wysyłane do poszczególnych urządzeń.

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

### <a name="read-logs-from-azure-event-hubs"></a>Odczytywanie dzienników z usługi Azure Event Hubs

Po skonfigurowaniu rejestrowania zdarzeń za pomocą ustawień diagnostycznych można tworzyć aplikacje odczytywane dzienniki, dzięki czemu można podejmować działania na podstawie zawartych w nich informacji. Ten przykładowy kod pobiera dzienniki z centrum zdarzeń:

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

## <a name="use-azure-resource-health"></a>Korzystanie z kondycji zasobów platformy Azure

Użyj usługi Azure Resource Health, aby monitorować, czy centrum IoT jest uruchomione. Można również dowiedzieć się, czy regionalna awaria ma wpływ na kondycję centrum IoT Hub. Aby poznać szczegółowe informacje o stanie kondycji usługi Azure IoT Hub, zalecamy [korzystanie z usługi Azure Monitor.](#use-azure-monitor)

Usługa Azure IoT Hub wskazuje kondycję na poziomie regionalnym. Jeśli regionalna awaria wpływa na centrum IoT hub, stan kondycji jest wyświetlany jako **Nieznany.** Aby dowiedzieć się więcej, zobacz [Typy zasobów i kontrole kondycji zasobów platformy Azure](../service-health/resource-health-checks-resource-types.md).

Aby sprawdzić kondycję centrów IoT, wykonaj następujące kroki:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Przejdź do stanu**kondycji kondycji** **usługi** > .

3. Z listy rozwijanej wybierz subskrypcję, a następnie wybierz **pozycję Centrum IoT** jako typ zasobu.

Aby dowiedzieć się więcej o interpretowania danych dotyczących kondycji, zobacz [Omówienie kondycji zasobów platformy Azure](../service-health/resource-health-overview.md).

## <a name="next-steps"></a>Następne kroki

* [Opis metryk usługi IoT Hub](iot-hub-metrics.md)
* [Zdalne monitorowanie i powiadomienia IoT za pomocą usługi Azure Logic Apps łączącej centrum IoT i skrzynkę pocztową](iot-hub-monitoring-notifications-with-azure-logic-apps.md)
