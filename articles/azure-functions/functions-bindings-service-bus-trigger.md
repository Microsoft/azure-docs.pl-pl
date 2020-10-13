---
title: Wyzwalacz Azure Service Bus dla Azure Functions
description: Dowiedz się, jak uruchamiać funkcję platformy Azure, gdy są tworzone Azure Service Bus komunikaty.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: cd0b73dd22e5e2cab720bb1a33e58e25e517b1f6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90605043"
---
# <a name="azure-service-bus-trigger-for-azure-functions"></a>Wyzwalacz Azure Service Bus dla Azure Functions

Użyj wyzwalacza Service Bus, aby odpowiedzieć na komunikaty z kolejki Service Bus lub tematu. Począwszy od rozszerzenia wersja 3.1.0, można wyzwolić w kolejce lub temacie z włączoną obsługą sesji.

Aby uzyskać informacje na temat konfiguracji i szczegółów konfiguracji, zobacz [Omówienie](functions-bindings-service-bus.md).

## <a name="example"></a>Przykład

# <a name="c"></a>[C#](#tab/csharp)

Poniższy przykład pokazuje [funkcję języka C#](functions-dotnet-class-library.md) , która odczytuje [metadane komunikatów](#message-metadata) i rejestruje komunikat Service Bus kolejki:

```cs
[FunctionName("ServiceBusQueueTriggerCSharp")]                    
public static void Run(
    [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] 
    string myQueueItem,
    Int32 deliveryCount,
    DateTime enqueuedTimeUtc,
    string messageId,
    ILogger log)
{
    log.LogInformation($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
    log.LogInformation($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.LogInformation($"DeliveryCount={deliveryCount}");
    log.LogInformation($"MessageId={messageId}");
}
```

# <a name="c-script"></a>[Skrypt C#](#tab/csharp-script)

Poniższy przykład przedstawia powiązanie wyzwalacza Service Bus w *function.js* pliku i [funkcji skryptu języka C#](functions-reference-csharp.md) , która używa powiązania. Funkcja odczytuje [metadane komunikatów](#message-metadata) i rejestruje komunikat Service Bus kolejki.

Oto dane powiązania w *function.js* pliku:

```json
{
"bindings": [
    {
    "queueName": "testqueue",
    "connection": "MyServiceBusConnection",
    "name": "myQueueItem",
    "type": "serviceBusTrigger",
    "direction": "in"
    }
],
"disabled": false
}
```

Oto kod skryptu w języku C#:

```cs
using System;

public static void Run(string myQueueItem,
    Int32 deliveryCount,
    DateTime enqueuedTimeUtc,
    string messageId,
    TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");

    log.Info($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.Info($"DeliveryCount={deliveryCount}");
    log.Info($"MessageId={messageId}");
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

W poniższym przykładzie pokazano powiązanie wyzwalacza Service Bus w *function.js* pliku oraz [funkcja języka JavaScript](functions-reference-node.md) , która używa powiązania. Funkcja odczytuje [metadane komunikatów](#message-metadata) i rejestruje komunikat Service Bus kolejki. 

Oto dane powiązania w *function.js* pliku:

```json
{
"bindings": [
    {
    "queueName": "testqueue",
    "connection": "MyServiceBusConnection",
    "name": "myQueueItem",
    "type": "serviceBusTrigger",
    "direction": "in"
    }
],
"disabled": false
}
```

Oto kod skryptu JavaScript:

```javascript
module.exports = function(context, myQueueItem) {
    context.log('Node.js ServiceBus queue trigger function processed message', myQueueItem);
    context.log('EnqueuedTimeUtc =', context.bindingData.enqueuedTimeUtc);
    context.log('DeliveryCount =', context.bindingData.deliveryCount);
    context.log('MessageId =', context.bindingData.messageId);
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

Poniższy przykład ilustruje sposób odczytywania Service Bus komunikatu kolejki za pośrednictwem wyzwalacza.

Powiązanie Service Bus jest zdefiniowane w *function.jsw* przypadku, gdy *Typ* jest ustawiony na `serviceBusTrigger` .

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "msg",
      "type": "serviceBusTrigger",
      "direction": "in",
      "queueName": "inputqueue",
      "connection": "AzureServiceBusConnectionString"
    }
  ]
}
```

Kod w * _ \_ init_ \_ . PR* deklaruje parametr AS `func.ServiceBusMessage` , który umożliwia odczytywanie komunikatu w kolejce w funkcji.

```python
import azure.functions as func

import logging
import json

def main(msg: func.ServiceBusMessage):
    logging.info('Python ServiceBus queue trigger processed message.')

    result = json.dumps({
        'message_id': msg.message_id,
        'body': msg.get_body().decode('utf-8'),
        'content_type': msg.content_type,
        'expiration_time': msg.expiration_time,
        'label': msg.label,
        'partition_key': msg.partition_key,
        'reply_to': msg.reply_to,
        'reply_to_session_id': msg.reply_to_session_id,
        'scheduled_enqueue_time': msg.scheduled_enqueue_time,
        'session_id': msg.session_id,
        'time_to_live': msg.time_to_live,
        'to': msg.to,
        'user_properties': msg.user_properties,
        'metadata' : msg.metadata
    })

    logging.info(result)
```

# <a name="java"></a>[Java](#tab/java)

Poniższa funkcja języka Java używa `@ServiceBusQueueTrigger` adnotacji z [biblioteki środowiska uruchomieniowego Java Functions](/java/api/overview/azure/functions/runtime) do opisywania konfiguracji dla wyzwalacza kolejki Service Bus. Funkcja poniesie komunikat umieszczony w kolejce i dodaje ją do dzienników.

```java
@FunctionName("sbprocessor")
 public void serviceBusProcess(
    @ServiceBusQueueTrigger(name = "msg",
                             queueName = "myqueuename",
                             connection = "myconnvarname") String message,
   final ExecutionContext context
 ) {
     context.getLogger().info(message);
 }
```

Funkcje języka Java mogą być również wyzwalane, gdy komunikat zostanie dodany do tematu Service Bus. Poniższy przykład używa `@ServiceBusTopicTrigger` adnotacji, aby opisać konfigurację wyzwalacza.

```java
@FunctionName("sbtopicprocessor")
    public void run(
        @ServiceBusTopicTrigger(
            name = "message",
            topicName = "mytopicname",
            subscriptionName = "mysubscription",
            connection = "ServiceBusConnection"
        ) String message,
        final ExecutionContext context
    ) {
        context.getLogger().info(message);
    }
```

---

## <a name="attributes-and-annotations"></a>Atrybuty i adnotacje

# <a name="c"></a>[C#](#tab/csharp)

W [bibliotekach klas języka C#](functions-dotnet-class-library.md)Użyj następujących atrybutów, aby skonfigurować wyzwalacz Service Bus:

* [ServiceBusTriggerAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusTriggerAttribute.cs)

  Konstruktor atrybutu przyjmuje nazwę kolejki lub temat i subskrypcję. W Azure Functions wersja 1. x można także określić prawa dostępu do połączenia. Jeśli nie określisz praw dostępu, wartość domyślna to `Manage` . Aby uzyskać więcej informacji, zobacz sekcję [wyzwalacz-konfiguracja](#configuration) .

  Oto przykład, który pokazuje atrybut używany z parametrem ciągu:

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue")] string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  Ponieważ `Connection` Właściwość nie jest zdefiniowana, funkcja szuka ustawienia aplikacji o nazwie `AzureWebJobsServiceBus` , która jest domyślną nazwą parametrów połączenia Service Bus. Możesz również ustawić `Connection` Właściwość, aby określić nazwę ustawienia aplikacji, która zawiera Service Bus parametry połączenia do użycia, jak pokazano w poniższym przykładzie:

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] 
      string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  Aby zapoznać się z kompletnym przykładem, zobacz [wyzwalacz-example](#example).

* [ServiceBusAccountAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAccountAttribute.cs)

  Zapewnia inny sposób określania konta Service Bus, które ma być używane. Konstruktor przyjmuje nazwę ustawienia aplikacji, która zawiera Service Bus parametry połączenia. Ten atrybut może być stosowany na poziomie parametru, metody lub klasy. W poniższym przykładzie przedstawiono poziom klasy i poziom metody:

  ```csharp
  [ServiceBusAccount("ClassLevelServiceBusAppSetting")]
  public static class AzureFunctions
  {
      [ServiceBusAccount("MethodLevelServiceBusAppSetting")]
      [FunctionName("ServiceBusQueueTriggerCSharp")]
      public static void Run(
          [ServiceBusTrigger("myqueue", AccessRights.Manage)] 
          string myQueueItem, ILogger log)
  {
      ...
  }
  ```

Konto Service Bus, które ma być używane, jest określane w następującej kolejności:

* `ServiceBusTrigger` `Connection` Właściwość atrybutu.
* `ServiceBusAccount`Atrybut zastosowany do tego samego parametru, który jest `ServiceBusTrigger` atrybutem.
* `ServiceBusAccount`Atrybut zastosowany do funkcji.
* `ServiceBusAccount`Atrybut zastosowany do klasy.
* Ustawienie aplikacji "AzureWebJobsServiceBus".

# <a name="c-script"></a>[Skrypt C#](#tab/csharp-script)

Atrybuty nie są obsługiwane przez skrypt języka C#.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Atrybuty nie są obsługiwane przez język JavaScript.

# <a name="python"></a>[Python](#tab/python)

Atrybuty nie są obsługiwane przez język Python.

# <a name="java"></a>[Java](#tab/java)

`ServiceBusQueueTrigger`Adnotacja pozwala utworzyć funkcję, która jest uruchamiana podczas tworzenia komunikatu Service Bus kolejki. Dostępne opcje konfiguracji obejmują nazwę kolejki i nazwę parametrów połączenia.

`ServiceBusTopicTrigger`Adnotacja pozwala wyznaczyć temat i subskrypcję, aby wskazać, jakie dane są wyzwalane przez funkcję.

Zobacz [przykład](#example) wyzwalacza, aby uzyskać więcej szczegółów.

---

## <a name="configuration"></a>Konfiguracja

W poniższej tabeli objaśniono właściwości konfiguracji powiązań, które zostały ustawione w *function.js* pliku i `ServiceBusTrigger` atrybutu.

|function.jswłaściwości | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**Wprowadź** | nie dotyczy | Musi być ustawiona na wartość "serviceBusTrigger". Ta właściwość jest ustawiana automatycznie podczas tworzenia wyzwalacza w Azure Portal.|
|**wskazywa** | nie dotyczy | Musi być ustawiona na wartość "in". Ta właściwość jest ustawiana automatycznie podczas tworzenia wyzwalacza w Azure Portal. |
|**Nazwij** | nie dotyczy | Nazwa zmiennej, która reprezentuje komunikat kolejki lub tematu w kodzie funkcji. |
|**Zmienną QueueName**|**Zmienną QueueName**|Nazwa kolejki do monitorowania.  Ustawiaj tylko w przypadku monitorowania kolejki, a nie dla tematu.
|**temat**|**Temat**|Nazwa tematu do monitorowania. Ustawiaj tylko w przypadku monitorowania tematu, a nie dla kolejki.|
|**subscriptionName**|**SubscriptionName**|Nazwa subskrypcji do monitorowania. Ustawiaj tylko w przypadku monitorowania tematu, a nie dla kolejki.|
|**połączenia**|**Połączenie**|Nazwa ustawienia aplikacji, która zawiera Service Bus parametry połączenia do użycia dla tego powiązania. Jeśli nazwa ustawienia aplikacji zaczyna się od "AzureWebJobs", można określić tylko resztę nazwy. Jeśli na przykład ustawisz wartość `connection` "MyServiceBus", środowisko uruchomieniowe funkcji szuka ustawienia aplikacji o nazwie "AzureWebJobsMyServiceBus". Jeśli pozostawisz `connection` puste, środowisko uruchomieniowe funkcji używa domyślnych parametrów połączenia Service Bus w ustawieniu aplikacji o nazwie "AzureWebJobsServiceBus".<br><br>Aby uzyskać parametry połączenia, wykonaj kroki opisane w sekcji [pobieranie poświadczeń zarządzania](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string). Parametry połączenia muszą należeć do Service Bus przestrzeni nazw, a nie ograniczone do określonej kolejki lub tematu. |
|**accessRights**|**Dostęp**|Prawa dostępu do parametrów połączenia. Dostępne wartości to `manage` i `listen` . Wartość domyślna to `manage` , co oznacza, że `connection` ma uprawnienie do **zarządzania** . W przypadku używania parametrów połączenia, które nie mają uprawnienia do **zarządzania** , ustaw wartość `accessRights` "nasłuchuj". W przeciwnym razie środowisko uruchomieniowe funkcji może zakończyć się niepowodzeniem podczas próby wykonania operacji, które wymagają uprawnień do zarządzania. W Azure Functions w wersji 2. x lub nowszej ta właściwość nie jest dostępna, ponieważ Najnowsza wersja zestawu Service Bus SDK nie obsługuje operacji zarządzania.|
|**isSessionsEnabled**|**IsSessionsEnabled**|`true` w przypadku nawiązywania połączenia z kolejką lub subskrypcją [obsługującą sesję](../service-bus-messaging/message-sessions.md) . `false` w przeciwnym razie jest to wartość domyślna.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Użycie

# <a name="c"></a>[C#](#tab/csharp)

Następujące typy parametrów są dostępne dla komunikatu kolejki lub tematu:

* `string` — Jeśli komunikat ma wartość Text.
* `byte[]` — Przydatne w przypadku danych binarnych.
* Niestandardowy typ — Jeśli komunikat zawiera kod JSON, Azure Functions próbuje zdeserializować danych JSON.
* `BrokeredMessage` -Zawiera deserializowany komunikat z metodą [BrokeredMessage. GetBody \<T> ()](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody?view=azure-dotnet#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1) .
* [`MessageReceiver`](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver?view=azure-dotnet) — Służy do odbierania i potwierdzania komunikatów z kontenera komunikatów (wymagane, gdy [`autoComplete`](functions-bindings-service-bus-output.md#hostjson-settings) jest ustawiony na `false` ).

Te typy parametrów są przeznaczone dla Azure Functions wersja 1. x; dla 2. x i wyższych, użyj [`Message`](/dotnet/api/microsoft.azure.servicebus.message) zamiast `BrokeredMessage` .

# <a name="c-script"></a>[Skrypt C#](#tab/csharp-script)

Następujące typy parametrów są dostępne dla komunikatu kolejki lub tematu:

* `string` — Jeśli komunikat ma wartość Text.
* `byte[]` — Przydatne w przypadku danych binarnych.
* Niestandardowy typ — Jeśli komunikat zawiera kod JSON, Azure Functions próbuje zdeserializować danych JSON.
* `BrokeredMessage` -Zawiera deserializowany komunikat z metodą [BrokeredMessage. GetBody \<T> ()](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody?view=azure-dotnet#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1) .

Te parametry dotyczą Azure Functions wersji 1. x; dla 2. x i wyższych, użyj [`Message`](/dotnet/api/microsoft.azure.servicebus.message) zamiast `BrokeredMessage` .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Dostęp do kolejki lub komunikatu tematu za pomocą programu `context.bindings.<name from function.json>` . Wiadomość Service Bus jest przenoszona do funkcji jako obiekt ciągu lub JSON.

# <a name="python"></a>[Python](#tab/python)

Komunikat kolejki jest dostępny dla funkcji za pośrednictwem parametru, który został określony jako `func.ServiceBusMessage` . Wiadomość Service Bus jest przenoszona do funkcji jako obiekt ciągu lub JSON.

# <a name="java"></a>[Java](#tab/java)

Service Bus komunikat przychodzący jest dostępny za pośrednictwem `ServiceBusQueueMessage` `ServiceBusTopicMessage` parametru lub.

[Zobacz przykład, aby uzyskać szczegółowe informacje](#example).

---

## <a name="poison-messages"></a>Trujące komunikaty

Obsługa skażonych komunikatów nie może być kontrolowana ani skonfigurowana w Azure Functions. Service Bus obsługuje trujące komunikaty.

## <a name="peeklock-behavior"></a>Zachowanie PeekLock

Środowisko uruchomieniowe funkcji odbiera komunikat w [trybie PeekLock](../service-bus-messaging/service-bus-performance-improvements.md#receive-mode). Wywołuje `Complete` komunikat, jeśli funkcja zakończy się pomyślnie, lub wywołuje, `Abandon` Jeśli funkcja się nie powiedzie. Jeśli funkcja działa dłużej niż `PeekLock` limit czasu, blokada zostanie automatycznie odnowiona, o ile funkcja jest uruchomiona. 

`maxAutoRenewDuration`Można go skonfigurować w *host.jsna*, który jest mapowany na [OnMessageOptions. MaxAutoRenewDuration](/dotnet/api/microsoft.azure.servicebus.messagehandleroptions.maxautorenewduration?view=azure-dotnet). Maksymalna dozwolona wartość tego ustawienia to 5 minut zgodnie z dokumentacją Service Bus, podczas gdy można zwiększyć limit czasu dla funkcji z wartości domyślnej wynoszącej 5 minut do 10 minut. W przypadku funkcji Service Bus nie chcesz tego robić, ponieważ Przekroczono limit Service Bus odnowienia.

## <a name="message-metadata"></a>Metadane komunikatów

Wyzwalacz Service Bus zawiera kilka [właściwości metadanych](./functions-bindings-expressions-patterns.md#trigger-metadata). Te właściwości mogą służyć jako część wyrażeń powiązań w innych powiązaniach lub jako parametry w kodzie. Te właściwości są członkami klasy [Message](/dotnet/api/microsoft.azure.servicebus.message?view=azure-dotnet) .

|Właściwość|Type|Opis|
|--------|----|-----------|
|`ContentType`|`string`|Identyfikator typu zawartości używany przez nadawcę i odbiorcę dla logiki specyficznej dla aplikacji.|
|`CorrelationId`|`string`|Identyfikator korelacji.|
|`DeadLetterSource`|`string`|Źródło utraconych wiadomości.|
|`DeliveryCount`|`Int32`|Liczba dostaw.|
|`EnqueuedTimeUtc`|`DateTime`|Czas w kolejce w formacie UTC.|
|`ExpiresAtUtc`|`DateTime`|Czas wygaśnięcia w formacie UTC.|
|`Label`|`string`|Etykieta specyficzna dla aplikacji.|
|`MessageId`|`string`|Wartość zdefiniowana przez użytkownika, która Service Bus może służyć do identyfikowania zduplikowanych komunikatów, jeśli jest włączona.|
|`MessageReceiver`|`MessageReceiver`|Service Bus odbiorca wiadomości. Może służyć do porzucania, kończenia lub utraconia wiadomości.|
|`MessageSession`|`MessageSession`|Odbiorca wiadomości przeznaczony dla kolejek i tematów z włączoną obsługą sesji.|
|`ReplyTo`|`string`|Odpowiedź na adres kolejki.|
|`SequenceNumber`|`long`|Unikatowy numer przypisany do komunikatu przez Service Bus.|
|`To`|`string`|Adres do wysłania.|
|`UserProperties`|`IDictionary<string, object>`|Właściwości ustawione przez nadawcę.|

Zobacz [przykłady kodu](#example) , które używają tych właściwości wcześniej w tym artykule.

## <a name="next-steps"></a>Następne kroki

- [Wysyłanie komunikatów Azure Service Bus z Azure Functions (powiązanie danych wyjściowych)](./functions-bindings-service-bus-output.md)
