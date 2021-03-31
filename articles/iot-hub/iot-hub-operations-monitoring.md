---
title: Monitorowanie operacji IoT Hub platformy Azure (przestarzałe) | Microsoft Docs
description: Jak używać monitorowania operacji IoT Hub platformy Azure do monitorowania stanu operacji w centrum IoT w czasie rzeczywistym.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: robinsh
ms.custom: amqp, devx-track-csharp
ms.openlocfilehash: 045d5693c4388c6285bc6983ac2a385ceac9f6d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94408128"
---
# <a name="iot-hub-operations-monitoring-deprecated"></a>Monitorowanie operacji IoT Hub (przestarzałe)

Monitorowanie operacji IoT Hub umożliwia monitorowanie stanu operacji w centrum IoT w czasie rzeczywistym. IoT Hub śledzi zdarzenia w kilku kategoriach operacji. Możesz zrezygnować z wysyłania zdarzeń z jednej lub kilku kategorii do punktu końcowego Centrum IoT Hub w celu przetworzenia. Można monitorować dane pod kątem błędów lub konfigurować bardziej złożone przetwarzanie na podstawie wzorców danych.

>[!NOTE]
>**Monitorowanie operacji IoT Hub jest przestarzałe i zostało usunięte z IoT Hub 10 marca 2019**. Aby monitorować operacje i kondycję IoT Hub, zobacz [IoT Hub monitorowania](monitor-iot-hub.md). Aby uzyskać więcej informacji na temat osi czasu wycofania, zobacz [monitorowanie rozwiązań usługi Azure IoT za pomocą Azure monitor i Azure Resource Health](https://azure.microsoft.com/blog/monitor-your-azure-iot-solutions-with-azure-monitor-and-azure-resource-health).

IoT Hub monitoruje sześć kategorii zdarzeń:

* Operacje tożsamości urządzeń
* Dane telemetryczne urządzenia
* Komunikaty z chmury do urządzenia
* Połączenia
* Operacje przekazywania plików
* Routing komunikatów

> [!IMPORTANT]
> Monitorowanie operacji IoT Hub nie gwarantuje niezawodnej lub uporządkowanej dostawy zdarzeń. W zależności od IoT Hub podstawowej infrastruktury niektóre zdarzenia mogą zostać utracone lub dostarczone poza kolejnością. Przy użyciu monitorowania operacji można generować alerty na podstawie sygnałów dotyczących błędów, takich jak nieudane próby połączenia, lub odłączenia o wysokiej częstotliwości dla konkretnych urządzeń. Nie należy polegać na zdarzeniach monitorowania operacji, aby utworzyć spójny magazyn dla stanu urządzenia, np. śledzenie magazynu połączone lub rozłączone z urządzeniem. 

## <a name="how-to-enable-operations-monitoring"></a>Jak włączyć monitorowanie operacji

1. Utwórz centrum IoT. Instrukcje dotyczące sposobu tworzenia Centrum IoT można znaleźć [w przewodniku wprowadzenie.](quickstart-send-telemetry-dotnet.md)

2. Otwórz blok Centrum IoT. W tym miejscu kliknij pozycję **monitorowanie operacji**.

    ![Dostęp do konfiguracji monitorowania operacji w portalu](./media/iot-hub-operations-monitoring/enable-OM-1.png)

3. Wybierz kategorie monitorowania, które chcesz monitorować, a następnie kliknij przycisk **Zapisz**. Zdarzenia są dostępne do odczytu z punktu końcowego zgodnego z centrum zdarzeń wymienionym w obszarze **ustawienia monitorowania**. IoT Hub punkt końcowy jest wywoływany `messages/operationsmonitoringevents` .

    ![Konfigurowanie monitorowania operacji w centrum IoT Hub](./media/iot-hub-operations-monitoring/enable-OM-2.png)

> [!NOTE]
> Wybranie opcji monitorowanie **pełne** dla kategorii **połączenia** powoduje IoT Hub generowanie dodatkowych komunikatów diagnostycznych. W przypadku wszystkich innych kategorii ustawienie **pełne** zmienia ilość informacji IoT Hub uwzględnionych w każdym komunikacie o błędzie.

## <a name="event-categories-and-how-to-use-them"></a>Kategorie zdarzeń i sposoby ich używania

Każda kategoria monitorowania operacji śledzi inny typ interakcji z IoT Hub, a każda kategoria monitorowania ma schemat, który definiuje, jak zdarzenia w tej kategorii są strukturalne.

### <a name="device-identity-operations"></a>Operacje tożsamości urządzeń

Kategoria operacje tożsamości urządzenia śledzi błędy występujące podczas próby utworzenia, zaktualizowania lub usunięcia wpisu w rejestrze tożsamości Centrum IoT. Śledzenie tej kategorii jest przydatne w scenariuszach aprowizacji.

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

### <a name="device-telemetry"></a>Dane telemetryczne urządzenia

Kategoria telemetrii urządzenia śledzi błędy występujące w centrum IoT i są powiązane z potokiem telemetrii. Ta kategoria zawiera błędy występujące podczas wysyłania zdarzeń telemetrii (takich jak ograniczenie przepustowości) i otrzymywanie zdarzeń telemetrii (na przykład nieautoryzowanego czytnika). Ta kategoria nie może przechwytywać błędów spowodowanych przez kod uruchomiony na urządzeniu.

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

### <a name="cloud-to-device-commands"></a>Polecenia chmura-urządzenie

Kategoria poleceń z chmury do urządzenia śledzi błędy występujące w centrum IoT Hub i powiązane z potokiem komunikatów z chmury do urządzenia. Ta kategoria zawiera błędy występujące podczas wysyłania komunikatów z chmury do urządzeń (takich jak nieautoryzowany nadawca), otrzymywania komunikatów z chmury do urządzeń (takich jak liczba przekroczeń) i otrzymywania opinii z chmury do urządzeń (takich jak wygasłe informacje zwrotne). Ta kategoria nie przechwytuje błędów z urządzenia, które nieprawidłowo obsługuje komunikat z chmury do urządzenia, jeśli komunikat z chmury do urządzenia został pomyślnie dostarczony.

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

Kategoria połączenia śledzi błędy występujące, gdy urządzenia nawiązują połączenie lub rozłączą się z Centrum IoT. Śledzenie tej kategorii jest przydatne do identyfikowania nieautoryzowanych prób połączenia i śledzenia po utracie połączenia w przypadku urządzeń z niską łącznością.

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

Kategoria przekazywanie plików śledzi błędy występujące w centrum IoT Hub i są powiązane z funkcją przekazywania plików. Ta kategoria obejmuje:

* Błędy występujące przy użyciu identyfikatora URI sygnatury dostępu współdzielonego, na przykład kiedy wygasa przed powiadomieniem centrum o ukończonym przekazywaniu przez urządzenie.

* Przekazywanie zgłoszone przez urządzenie nie powiodło się.

* Błędy występujące, gdy podczas tworzenia komunikatu powiadomienia IoT Hub nie znaleziono pliku w magazynie.

Ta kategoria nie może przechwytywać błędów, które są bezpośrednio wykonywane, gdy urządzenie przekazuje plik do magazynu.

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

Kategoria routingu wiadomości śledzi błędy występujące podczas oceny trasy komunikatów i kondycji punktu końcowego, jak zostało to postrzegane przez IoT Hub. Ta kategoria zawiera zdarzenia, takie jak kiedy reguła ma wartość "undefined", gdy IoT Hub oznacza punkt końcowy jako martwy i wszystkie inne błędy odebrane z punktu końcowego. Ta kategoria nie zawiera określonych błędów dotyczących samych komunikatów (takich jak błędy ograniczania urządzeń), które są raportowane w kategorii "dane telemetryczne urządzenia".

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

## <a name="connect-to-the-monitoring-endpoint"></a>Nawiązywanie połączenia z punktem końcowym monitorowania

Punkt końcowy monitorowania w usłudze IoT Hub jest punktem końcowym zgodnym z centrum zdarzeń. Aby odczytać komunikaty monitorowania z tego punktu końcowego, można użyć dowolnego mechanizmu, który współpracuje z Event Hubs. Poniższy przykład tworzy podstawowy czytnik, który nie jest odpowiedni do wdrożenia o wysokiej przepływności. Więcej informacji na temat przetwarzania komunikatów z usługi Event Hubs znajduje się w samouczku [Rozpoczynanie pracy z usługą Event Hubs](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md).

Aby nawiązać połączenie z punktem końcowym monitorowania, potrzebne są parametry połączenia i nazwa punktu końcowego. Poniższe kroki pokazują, jak znaleźć wymagane wartości w portalu:

1. W portalu przejdź do bloku zasobów IoT Hub.

2. Wybierz pozycję **monitorowanie operacji** i zanotuj wartości zgodne z centrum zdarzeń **Nazwa** i **punkt końcowy zgodne z centrum zdarzeń** :

    ![Wartości punktów końcowych zgodnych z centrum zdarzeń](./media/iot-hub-operations-monitoring/monitoring-endpoint.png)

3. Wybierz pozycję **zasady dostępu współdzielonego**, a następnie wybierz pozycję **Usługa**. Zanotuj wartość **klucza podstawowego** :

    ![Klucz podstawowy zasad dostępu współdzielonego usługi](./media/iot-hub-operations-monitoring/service-key.png)

Następujący przykładowy kod w języku C# jest pobierany z aplikacji konsolowej **klasycznej pulpitu** programu Visual Studio systemu Windows. Projekt ma zainstalowany pakiet NuGet **windowsazure. ServiceBus** .

* Zastąp symbol zastępczy parametrów połączenia parametrami połączenia, które korzystają z **punktu końcowego zgodnego z centrum zdarzeń** i wartości **klucza podstawowego** usługi, które zostały zanotowane wcześniej, jak pokazano w następującym przykładzie:

    ```csharp
    "Endpoint={your Event Hub-compatible endpoint};SharedAccessKeyName=service;SharedAccessKey={your service primary key value}"
    ```

* Zamień symbol zastępczy nazwy punktu końcowego monitorowania na zanotowaną wcześniej wartość **nazwy zgodną z centrum zdarzeń** .

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

Aby dowiedzieć się więcej o używaniu Azure Monitor do monitorowania IoT Hub, zobacz:

* [Monitorowanie usługi IoT Hub](monitor-iot-hub.md)

* [Migrowanie z monitorowania IoT Hub operacji do Azure Monitor](iot-hub-migrate-to-diagnostics-settings.md)