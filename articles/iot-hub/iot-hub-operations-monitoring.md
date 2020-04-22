---
title: Monitorowanie operacji usługi Azure IoT Hub (przestarzałe) | Dokumenty firmy Microsoft
description: Jak używać monitorowania operacji usługi Azure IoT Hub do monitorowania stanu operacji w centrum IoT w czasie rzeczywistym.
author: nberdy
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: nberdy
ms.custom: amqp
ms.openlocfilehash: edbc3431c860794c7cd1dd8e5011c0d7d11d692d
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732235"
---
# <a name="iot-hub-operations-monitoring-deprecated"></a>Monitorowanie operacji usługi IoT Hub (przestarzałe)

Monitorowanie operacji usługi IoT Hub umożliwia monitorowanie stanu operacji w centrum IoT hub w czasie rzeczywistym. Centrum IoT hub śledzi zdarzenia w kilku kategoriach operacji. Możesz zdecydować się na wysyłanie zdarzeń z jednej lub więcej kategorii do punktu końcowego centrum IoT hub do przetwarzania. Można monitorować dane pod kątem błędów lub skonfigurować bardziej złożone przetwarzanie na podstawie wzorców danych.

>[!NOTE]
>Monitorowanie operacji usługi IoT Hub **jest przestarzałe i zostało usunięte z Usługi IoT Hub 10 marca 2019 r.** Aby zapoznać się z monitorowaniem operacji i kondycji usługi IoT Hub, zobacz [Monitorowanie kondycji usługi Azure IoT Hub i szybkie diagnozowanie problemów.](iot-hub-monitor-resource-health.md) Aby uzyskać więcej informacji na temat osi czasu wycofania, zobacz [Monitorowanie rozwiązań IoT platformy Azure za pomocą usługi Azure Monitor i usługi Azure Resource Health.](https://azure.microsoft.com/blog/monitor-your-azure-iot-solutions-with-azure-monitor-and-azure-resource-health)

Centrum IoT monitoruje sześć kategorii zdarzeń:

* Operacje tożsamości urządzenia
* Telemetria urządzenia
* Komunikaty z chmury do urządzenia
* Połączenia
* Operacje przekazywania plików
* Routing komunikatów

> [!IMPORTANT]
> Monitorowanie operacji usługi IoT Hub nie gwarantuje niezawodnego lub uporządkowanego dostarczania zdarzeń. W zależności od infrastruktury podstawowej usługi IoT Hub niektóre zdarzenia mogą zostać utracone lub dostarczone poza kolejnością. Monitorowanie operacji służy do generowania alertów na podstawie sygnałów błędów, takich jak nieudane próby połączenia lub rozłączenia o wysokiej częstotliwości dla określonych urządzeń. Nie należy polegać na zdarzeniach monitorowania operacji, aby utworzyć spójny magazyn dla stanu urządzenia, na przykład stan połączonego lub odłączonego sklepu urządzenia. 

## <a name="how-to-enable-operations-monitoring"></a>Jak włączyć monitorowanie operacji

1. Utwórz centrum IoT. Instrukcje dotyczące tworzenia centrum IoT hub można znaleźć w przewodniku [Wprowadzenie.](quickstart-send-telemetry-dotnet.md)

2. Otwórz ostrzu piasty IoT. Stamtąd kliknij pozycję **Monitorowanie operacji**.

    ![Konfiguracja monitorowania operacji dostępu w portalu](./media/iot-hub-operations-monitoring/enable-OM-1.png)

3. Wybierz kategorie monitorowania, które chcesz monitorować, a następnie kliknij przycisk **Zapisz**. Zdarzenia są dostępne do odczytu z punktu końcowego zgodnego z Centrum zdarzeń wymienionego w **ustawieniach monitorowania**. Punkt końcowy centrum IoT `messages/operationsmonitoringevents`hub jest wywoływany .

    ![Konfigurowanie monitorowania operacji w centrum IoT](./media/iot-hub-operations-monitoring/enable-OM-2.png)

> [!NOTE]
> Wybranie pełnej kontroli dla kategorii **Połączenia** powoduje, że Centrum IoT hub generuje dodatkowe komunikaty **diagnostyczne.** Dla wszystkich innych kategorii ustawienie **pełne** zmienia ilość informacji, które Centrum IoT zawiera w każdym komunikacie o błędzie.

## <a name="event-categories-and-how-to-use-them"></a>Kategorie zdarzeń i jak z nich korzystać

Każda kategoria monitorowania operacji śledzi inny typ interakcji z Centrum IoT, a każda kategoria monitorowania ma schemat, który definiuje strukturę zdarzeń w tej kategorii.

### <a name="device-identity-operations"></a>Operacje tożsamości urządzenia

Kategoria operacji tożsamości urządzenia śledzi błędy, które występują podczas próby utworzenia, aktualizacji lub usunięcia wpisu w rejestrze tożsamości centrum IoT. Śledzenie tej kategorii jest przydatne w przypadku scenariuszy inicjowania obsługi administracyjnej.

```json
{
    "time": "UTC timestamp",
    "operationName": "create",
    "category": "DeviceIdentityOperations",
    "level": "Error",
    "statusCode": 4XX,
    "statusDescription": "MessageDescription",
    "deviceId": "device-ID",
    "durationMs": 1234,
    "userAgent": "userAgent",
    "sharedAccessPolicy": "accessPolicy"
}
```

### <a name="device-telemetry"></a>Telemetria urządzenia

Kategoria telemetrii urządzenia śledzi błędy występujące w centrum IoT hub i są związane z potoku telemetrii. Ta kategoria zawiera błędy, które występują podczas wysyłania zdarzeń telemetrycznych (takich jak ograniczanie przepustowości) i odbieranie zdarzeń telemetrycznych (takich jak nieautoryzowany czytnik). Ta kategoria nie może przechwytyć błędów spowodowanych przez kod uruchomiony na samym urządzeniu.

```json
{
    "messageSizeInBytes": 1234,
    "batching": 0,
    "protocol": "Amqp",
    "authType": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
    "time": "UTC timestamp",
    "operationName": "ingress",
    "category": "DeviceTelemetry",
    "level": "Error",
    "statusCode": 4XX,
    "statusType": 4XX001,
    "statusDescription": "MessageDescription",
    "deviceId": "device-ID",
    "EventProcessedUtcTime": "UTC timestamp",
    "PartitionId": 1,
    "EventEnqueuedUtcTime": "UTC timestamp"
}
```

### <a name="cloud-to-device-commands"></a>Polecenia chmury do urządzenia

Kategoria poleceń chmury do urządzenia śledzi błędy występujące w centrum IoT hub i są związane z potokiem komunikatów z chmury do urządzenia. Ta kategoria obejmuje błędy, które występują podczas wysyłania komunikatów z chmury do urządzenia (takich jak nieautoryzowany nadawca), odbierania komunikatów z chmury do urządzenia (takich jak przekroczenie liczby dostarczania) i odbierania opinii z chmury do urządzenia (takich jak opinie wygasły). Ta kategoria nie wychwytuje błędów z urządzenia, które nieprawidłowo obsługuje komunikat chmury do urządzenia, jeśli komunikat z chmury do urządzenia został dostarczony pomyślnie.

```json
{
    "messageSizeInBytes": 1234,
    "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
    "deliveryAcknowledgement": 0,
    "protocol": "Amqp",
    "time": " UTC timestamp",
    "operationName": "ingress",
    "category": "C2DCommands",
    "level": "Error",
    "statusCode": 4XX,
    "statusType": 4XX001,
    "statusDescription": "MessageDescription",
    "deviceId": "device-ID",
    "EventProcessedUtcTime": "UTC timestamp",
    "PartitionId": 1,
    "EventEnqueuedUtcTime": "UTC timestamp"
}
```

### <a name="connections"></a>Połączenia

Kategoria połączeń śledzi błędy, które występują, gdy urządzenia łączą się lub rozłączają z centrum IoT hub. Śledzenie tej kategorii jest przydatne do identyfikowania nieautoryzowanych prób połączenia i śledzenia, gdy połączenie zostanie utracone dla urządzeń w obszarach słabej łączności.

```json
{
    "durationMs": 1234,
    "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
    "protocol": "Amqp",
    "time": " UTC timestamp",
    "operationName": "deviceConnect",
    "category": "Connections",
    "level": "Error",
    "statusCode": 4XX,
    "statusType": 4XX001,
    "statusDescription": "MessageDescription",
    "deviceId": "device-ID"
}
```

### <a name="file-uploads"></a>Operacje przekazywania plików

Kategoria przekazywania plików śledzi błędy występujące w centrum IoT i są związane z funkcją przekazywania plików. Kategoria ta obejmuje:

* Błędy występujące w identyfikatorze URI sygnatury dostępu Współdzielonego, na przykład po wygaśnięciu, zanim urządzenie powiadomi centrum zakończonego przekazywania.

* Nie powiodło się przesłania zgłoszone przez urządzenie.

* Błędy, które występują, gdy plik nie zostanie znaleziony w magazynie podczas tworzenia komunikatów powiadomień Usługi IoT Hub.

Ta kategoria nie może przechwytywać błędów, które występują bezpośrednio podczas przekazywania pliku do magazynu.

```json
{
    "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
    "protocol": "HTTP",
    "time": " UTC timestamp",
    "operationName": "ingress",
    "category": "fileUpload",
    "level": "Error",
    "statusCode": 4XX,
    "statusType": 4XX001,
    "statusDescription": "MessageDescription",
    "deviceId": "device-ID",
    "blobUri": "http//bloburi.com",
    "durationMs": 1234
}
```

### <a name="message-routing"></a>Routing komunikatów

Kategoria routingu wiadomości śledzi błędy występujące podczas oceny trasy wiadomości i kondycji punktu końcowego jako postrzegane przez Centrum IoT Hub. Ta kategoria obejmuje zdarzenia, takie jak gdy reguła ma wartość "niezdefiniowana", gdy centrum IoT oznacza punkt końcowy jako martwy, oraz wszelkie inne błędy odebrane z punktu końcowego. Ta kategoria nie zawiera określonych błędów dotyczących samych komunikatów (takich jak błędy ograniczania przepustowości urządzeń), które są zgłaszane w kategorii "telemetria urządzenia".

```json
{
    "messageSizeInBytes": 1234,
    "time": "UTC timestamp",
    "operationName": "ingress",
    "category": "routes",
    "level": "Error",
    "deviceId": "device-ID",
    "messageId": "ID of message",
    "routeName": "myroute",
    "endpointName": "myendpoint",
    "details": "ExternalEndpointDisabled"
}
```

## <a name="connect-to-the-monitoring-endpoint"></a>Łączenie się z punktem końcowym monitorowania

Punkt końcowy monitorowania w centrum IoT hub jest punktem końcowym zgodnym z Centrum zdarzeń. Można użyć dowolnego mechanizmu, który współpracuje z Centrum zdarzeń do odczytywania komunikatów monitorowania z tego punktu końcowego. Poniższy przykład tworzy podstawowy czytnik, który nie nadaje się do wdrożenia o wysokiej przepływności. Więcej informacji na temat przetwarzania komunikatów z usługi Event Hubs znajduje się w samouczku [Rozpoczynanie pracy z usługą Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md).

Aby połączyć się z punktem końcowym monitorowania, potrzebujesz ciągu połączenia i nazwy punktu końcowego. Poniższe kroki pokazują, jak znaleźć niezbędne wartości w portalu:

1. W portalu przejdź do bloku zasobu usługi IoT Hub.

2. Wybierz **pozycję Monitorowanie operacji**i zanotuj nazwę **zgodną z Centrum zdarzeń** i wartości punktów końcowych **zgodnych z Centrum zdarzeń:**

    ![Wartości punktów końcowych zgodnych z centrum zdarzeń](./media/iot-hub-operations-monitoring/monitoring-endpoint.png)

3. Wybierz pozycję **Zasady dostępu współdzielonego**, a następnie wybierz **usługę**. Zanotuj wartość **klucza podstawowego:**

    ![Klucz podstawowy zasad dostępu współdzielonego usługi](./media/iot-hub-operations-monitoring/service-key.png)

Poniższy przykład kodu języka C# jest pobierany z aplikacji konsoli klasycznego **pulpitu** systemu Visual Studio Windows Desktop C#. Projekt ma zainstalowany pakiet **WindowsAzure.ServiceBus** NuGet.

* Zastąp symbol zastępczy ciągu połączenia ciągiem połączenia, który używa wartości **klucza końcowego zgodnego z Centrum zdarzeń** i usługi **Podstawowej,** które zostały wcześniej odnotowane, jak pokazano w poniższym przykładzie:

    ```csharp
    "Endpoint={your Event Hub-compatible endpoint};SharedAccessKeyName=service;SharedAccessKey={your service primary key value}"
    ```

* Zastąp symbol zastępczy nazwy punktu końcowego monitorowania wartością **nazwy zgodną z Centrum zdarzeń,** którą wcześniej wspomniano.

```csharp
class Program
{
    static string connectionString = "{your monitoring endpoint connection string}";
    static string monitoringEndpointName = "{your monitoring endpoint name}";
    static EventHubClient eventHubClient;

    static void Main(string[] args)
    {
        Console.WriteLine("Monitoring. Press Enter key to exit.\n");

        eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, monitoringEndpointName);
        var d2cPartitions = eventHubClient.GetRuntimeInformation().PartitionIds;
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
            }
        }
    }
}
```

## <a name="next-steps"></a>Następne kroki

Aby dokładniej zbadać możliwości usługi IoT Hub, zobacz:

* [Przewodnik dla deweloperów usługi IoT Hub](iot-hub-devguide.md)

* [Wdrażanie rozwiązań SI na urządzeniach brzegowych przy użyciu usługi Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)