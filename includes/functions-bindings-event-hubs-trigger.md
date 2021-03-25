---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 03/05/2019
ms.author: cshoe
ms.openlocfilehash: 4bea0f6091c02f80e381217ab7894ec4ad90d0ed
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105104480"
---
Użyj wyzwalacza funkcji, aby odpowiedzieć na zdarzenie wysłane do strumienia zdarzeń centrum zdarzeń. Aby skonfigurować wyzwalacz, musisz mieć dostęp do odczytu do odpowiedniego centrum zdarzeń. Gdy funkcja jest wyzwalana, komunikat przeszedł do funkcji jest wpisywany jako ciąg.

## <a name="scaling"></a>Skalowanie

Każde wystąpienie funkcji wyzwalanej przez zdarzenie jest obsługiwane przez pojedyncze wystąpienie [klasy eventprocessorhost](/dotnet/api/microsoft.azure.eventhubs.processor) . Wyzwalacz (obsługiwany przez Event Hubs) gwarantuje, że tylko jedno wystąpienie [klasy eventprocessorhost](/dotnet/api/microsoft.azure.eventhubs.processor) może uzyskać dzierżawę dla danej partycji.

Rozważmy na przykład centrum zdarzeń w następujący sposób:

* 10 partycji
* zdarzenia 1 000 równomiernie rozłożone na wszystkie partycje, z 100 komunikatami w każdej partycji

Gdy funkcja jest włączona po raz pierwszy, istnieje tylko jedno wystąpienie funkcji. Wywołajmy pierwsze wystąpienie funkcji `Function_0` . `Function_0`Funkcja ma pojedyncze wystąpienie [klasy eventprocessorhost](/dotnet/api/microsoft.azure.eventhubs.processor) , które przechowuje dzierżawę na wszystkich dziesięciu partycjach. To wystąpienie odczytuje zdarzenia z partycji 0-9. Od tego momentu następuje jedna z następujących sytuacji:

* **Nowe wystąpienia funkcji nie są konieczne**: `Function_0` można przetworzyć wszystkie zdarzenia 1 000 przed zastosowaniem logiki skalowania funkcji. W takim przypadku wszystkie komunikaty 1 000 są przetwarzane przez program `Function_0` .

* **Dodawane jest dodatkowe wystąpienie funkcji**: jeśli logika skalowania funkcji określa, że program `Function_0` ma więcej komunikatów niż może przetworzyć, tworzone jest nowe wystąpienie aplikacji funkcji ( `Function_1` ). Ta nowa funkcja ma również skojarzone wystąpienie elementu [klasy eventprocessorhost](/dotnet/api/microsoft.azure.eventhubs.processor). Ponieważ podstawowe Event Hubs wykrywają, że nowe wystąpienie hosta próbuje odczytywać komunikaty, równoważenie obciążenia partycji w wystąpieniach hosta. Na przykład partycje 0-4 mogą być przypisane do `Function_0` i partycji 5-9 do `Function_1` .

* **Dodano więcej wystąpień funkcji**: jeśli logika skalowania funkcji określa, że oba `Function_0` i `Function_1` więcej komunikatów niż mogą przetwarzać, `Functions_N` tworzone są nowe wystąpienia aplikacji funkcji.  Aplikacje są tworzone w punkcie, w którym `N` jest większa niż liczba partycji centrum zdarzeń. W naszym przykładzie Event Hubs ponownie równoważenia obciążenia w partycjach, w tym przypadku między wystąpieniami `Function_0` .. `Functions_9` .

W miarę występowania skalowania `N` Liczba wystąpień jest większa niż liczba partycji centrum zdarzeń. Ten wzorzec służy do upewniania się, że wystąpienia [klasy eventprocessorhost](/dotnet/api/microsoft.azure.eventhubs.processor) są dostępne do uzyskania blokad w partycjach, gdy staną się dostępne z innych wystąpień. Opłata jest naliczana tylko za zasoby używane, gdy wystąpienie funkcji jest wykonywane. Innymi słowy, nie jest naliczana opłata za tę nadmierną obsługę.

Po zakończeniu wykonywania wszystkich funkcji (z błędami lub bez nich) punkty kontrolne są dodawane do skojarzonego konta magazynu. Po pomyślnym sprawdzeniu, wszystkie komunikaty 1 000 nigdy nie są pobierane ponownie.

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

Poniższy przykład pokazuje [funkcję języka C#](../articles/azure-functions/functions-dotnet-class-library.md) , która rejestruje treść komunikatu wyzwalacza centrum zdarzeń.

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    log.LogInformation($"C# function triggered to process a message: {myEventHubMessage}");
}
```

Aby uzyskać dostęp do [metadanych zdarzenia](#event-metadata) w kodzie funkcji, powiąż z obiektem [EVENTDATA](/dotnet/api/microsoft.servicebus.messaging.eventdata) (wymaga instrukcji using dla `Microsoft.Azure.EventHubs` ). Możesz również uzyskać dostęp do tych samych właściwości przy użyciu wyrażeń powiązania w podpisie metody.  W poniższym przykładzie pokazano dwa sposoby uzyskania tych samych danych:

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run(
    [EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] EventData myEventHubMessage,
    DateTime enqueuedTimeUtc,
    Int64 sequenceNumber,
    string offset,
    ILogger log)
{
    log.LogInformation($"Event: {Encoding.UTF8.GetString(myEventHubMessage.Body)}");
    // Metadata accessed by binding to EventData
    log.LogInformation($"EnqueuedTimeUtc={myEventHubMessage.SystemProperties.EnqueuedTimeUtc}");
    log.LogInformation($"SequenceNumber={myEventHubMessage.SystemProperties.SequenceNumber}");
    log.LogInformation($"Offset={myEventHubMessage.SystemProperties.Offset}");
    // Metadata accessed by using binding expressions in method parameters
    log.LogInformation($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.LogInformation($"SequenceNumber={sequenceNumber}");
    log.LogInformation($"Offset={offset}");
}
```

Do odbierania zdarzeń w partii, marka `string` lub `EventData` Tablica.  

> [!NOTE]
> W przypadku odbierania w partii nie można powiązać parametrów metody, takich jak w powyższym przykładzie z `DateTime enqueuedTimeUtc` i muszą otrzymać te dane z każdego `EventData` obiektu  

```cs
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] EventData[] eventHubMessages, ILogger log)
{
    foreach (var message in eventHubMessages)
    {
        log.LogInformation($"C# function triggered to process a message: {Encoding.UTF8.GetString(message.Body)}");
        log.LogInformation($"EnqueuedTimeUtc={message.SystemProperties.EnqueuedTimeUtc}");
    }
}
```

# <a name="c-script"></a>[Skrypt C#](#tab/csharp-script)

Poniższy przykład przedstawia powiązanie wyzwalacza centrum zdarzeń w *function.jsw* pliku i [funkcji skryptu języka C#](../articles/azure-functions/functions-reference-csharp.md) , która używa powiązania. Funkcja rejestruje treść komunikatu wyzwalacza centrum zdarzeń.

W poniższych przykładach pokazano Event Hubs powiązania danych w *function.js* pliku.

### <a name="version-2x-and-higher"></a>Wersja 2. x lub nowsza

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

### <a name="version-1x"></a>Wersja 1. x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Oto kod skryptu w języku C#:

```cs
using System;

public static void Run(string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# function triggered to process a message: {myEventHubMessage}");
}
```

Aby uzyskać dostęp do [metadanych zdarzenia](#event-metadata) w kodzie funkcji, powiąż z obiektem [EVENTDATA](/dotnet/api/microsoft.servicebus.messaging.eventdata) (wymaga instrukcji using dla `Microsoft.Azure.EventHubs` ). Możesz również uzyskać dostęp do tych samych właściwości przy użyciu wyrażeń powiązania w podpisie metody.  W poniższym przykładzie pokazano dwa sposoby uzyskania tych samych danych:

```cs
#r "Microsoft.Azure.EventHubs"

using System.Text;
using System;
using Microsoft.ServiceBus.Messaging;
using Microsoft.Azure.EventHubs;

public static void Run(EventData myEventHubMessage,
    DateTime enqueuedTimeUtc,
    Int64 sequenceNumber,
    string offset,
    TraceWriter log)
{
    log.Info($"Event: {Encoding.UTF8.GetString(myEventHubMessage.Body)}");
    log.Info($"EnqueuedTimeUtc={myEventHubMessage.SystemProperties.EnqueuedTimeUtc}");
    log.Info($"SequenceNumber={myEventHubMessage.SystemProperties.SequenceNumber}");
    log.Info($"Offset={myEventHubMessage.SystemProperties.Offset}");

    // Metadata accessed by using binding expressions
    log.Info($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.Info($"SequenceNumber={sequenceNumber}");
    log.Info($"Offset={offset}");
}
```

Aby odbierać zdarzenia w partii, Utwórz `string` lub `EventData` tablicę:

```cs
public static void Run(string[] eventHubMessages, TraceWriter log)
{
    foreach (var message in eventHubMessages)
    {
        log.Info($"C# function triggered to process a message: {message}");
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Poniższy przykład przedstawia powiązanie wyzwalacza centrum zdarzeń w *function.jsw* pliku oraz [funkcja języka JavaScript](../articles/azure-functions/functions-reference-node.md) , która używa powiązania. Funkcja odczytuje [metadane zdarzeń](#event-metadata) i rejestruje komunikat.

W poniższych przykładach pokazano Event Hubs powiązania danych w *function.js* pliku.

### <a name="version-2x-and-higher"></a>Wersja 2. x lub nowsza

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

### <a name="version-1x"></a>Wersja 1. x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Oto kod JavaScript:

```javascript
module.exports = function (context, myEventHubMessage) {
    context.log('Function triggered to process a message: ', myEventHubMessage);
    context.log('EnqueuedTimeUtc =', context.bindingData.enqueuedTimeUtc);
    context.log('SequenceNumber =', context.bindingData.sequenceNumber);
    context.log('Offset =', context.bindingData.offset);

    context.done();
};
```

Aby odbierać zdarzenia w partii, ustaw wartość `cardinality` `many` w *function.js* w pliku, jak pokazano w poniższych przykładach.

### <a name="version-2x-and-higher"></a>Wersja 2. x lub nowsza

```json
{
  "type": "eventHubTrigger",
  "name": "eventHubMessages",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "cardinality": "many",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

### <a name="version-1x"></a>Wersja 1. x

```json
{
  "type": "eventHubTrigger",
  "name": "eventHubMessages",
  "direction": "in",
  "path": "MyEventHub",
  "cardinality": "many",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Oto kod JavaScript:

```javascript
module.exports = function (context, eventHubMessages) {
    context.log(`JavaScript eventhub trigger function called for message array ${eventHubMessages}`);

    eventHubMessages.forEach((message, index) => {
        context.log(`Processed message ${message}`);
        context.log(`EnqueuedTimeUtc = ${context.bindingData.enqueuedTimeUtcArray[index]}`);
        context.log(`SequenceNumber = ${context.bindingData.sequenceNumberArray[index]}`);
        context.log(`Offset = ${context.bindingData.offsetArray[index]}`);
    });

    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

Poniższy przykład przedstawia powiązanie wyzwalacza centrum zdarzeń w *function.jsw* pliku i [funkcji języka Python](../articles/azure-functions/functions-reference-python.md) , która używa powiązania. Funkcja odczytuje [metadane zdarzeń](#event-metadata) i rejestruje komunikat.

W poniższych przykładach pokazano Event Hubs powiązania danych w *function.js* pliku.

```json
{
  "type": "eventHubTrigger",
  "name": "event",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Oto kod języka Python:

```python
import logging
import azure.functions as func


def main(event: func.EventHubEvent):
    logging.info(f'Function triggered to process a message: {event.get_body().decode()}')
    logging.info(f'  EnqueuedTimeUtc = {event.enqueued_time}')
    logging.info(f'  SequenceNumber = {event.sequence_number}')
    logging.info(f'  Offset = {event.offset}')

    # Metadata
    for key in event.metadata:
        logging.info(f'Metadata: {key} = {event.metadata[key]}')
```

# <a name="java"></a>[Java](#tab/java)

Poniższy przykład pokazuje powiązanie wyzwalacza centrum zdarzeń, które rejestruje treść komunikatu wyzwalacza centrum zdarzeń.

```java
@FunctionName("ehprocessor")
public void eventHubProcessor(
  @EventHubTrigger(name = "msg",
                  eventHubName = "myeventhubname",
                  connection = "myconnvarname") String message,
       final ExecutionContext context )
       {
          context.getLogger().info(message);
 }
```

 W [bibliotece środowiska uruchomieniowego funkcji Java](/java/api/overview/azure/functions/runtime)Użyj `EventHubTrigger` adnotacji w parametrach, których wartość pochodzi z centrum zdarzeń. Parametry z tymi adnotacjami powodują, że funkcja jest uruchamiana po nadejściu zdarzenia.  Tej adnotacji można używać w przypadku natywnych typów Java, Pojo lub wartości null przy użyciu `Optional<T>` .

 ---

## <a name="attributes-and-annotations"></a>Atrybuty i adnotacje

# <a name="c"></a>[C#](#tab/csharp)

W [bibliotekach klas języka C#](../articles/azure-functions/functions-dotnet-class-library.md), Użyj atrybutu [EventHubTriggerAttribute](https://github.com/Azure/azure-functions-eventhubs-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs/EventHubTriggerAttribute.cs) .

Konstruktor atrybutu przyjmuje nazwę centrum zdarzeń, nazwę grupy odbiorców i nazwę ustawienia aplikacji, które zawiera parametry połączenia... Więcej informacji o tych ustawieniach znajduje się w [sekcji Konfiguracja wyzwalacza](#configuration). Oto `EventHubTriggerAttribute` przykład atrybutu:

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    ...
}
```

Aby uzyskać pełny przykład, zobacz [przykład wyzwalacza-C#](#example).

# <a name="c-script"></a>[Skrypt C#](#tab/csharp-script)

Atrybuty nie są obsługiwane przez skrypt języka C#.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Atrybuty nie są obsługiwane przez język JavaScript.

# <a name="python"></a>[Python](#tab/python)

Atrybuty nie są obsługiwane przez język Python.

# <a name="java"></a>[Java](#tab/java)

W [bibliotece środowiska uruchomieniowego usługi Java Functions](/java/api/overview/azure/functions/runtime)Użyj adnotacji [EventHubTrigger](/java/api/com.microsoft.azure.functions.annotation.eventhubtrigger) w przypadku parametrów, których wartość pochodzi z centrum zdarzeń. Parametry z tymi adnotacjami powodują, że funkcja jest uruchamiana po nadejściu zdarzenia. Tej adnotacji można używać w przypadku natywnych typów Java, Pojo lub wartości null przy użyciu `Optional<T>` .

---

## <a name="configuration"></a>Konfigurowanie

W poniższej tabeli objaśniono właściwości konfiguracji powiązań, które zostały ustawione w *function.js* pliku i `EventHubTrigger` atrybutu.

|function.jswłaściwości | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**Wprowadź** | n/d | Musi być ustawiony na `eventHubTrigger` . Ta właściwość jest ustawiana automatycznie podczas tworzenia wyzwalacza w Azure Portal.|
|**wskazywa** | n/d | Musi być ustawiony na `in` . Ta właściwość jest ustawiana automatycznie podczas tworzenia wyzwalacza w Azure Portal. |
|**Nazwij** | n/d | Nazwa zmiennej, która reprezentuje element zdarzenia w kodzie funkcji. |
|**ścieżka** |**EventHubName** | Tylko funkcje 1. x. Nazwa centrum zdarzeń. Gdy nazwa centrum zdarzeń jest również obecna w parametrach połączenia, ta wartość zastępuje tę właściwość w czasie wykonywania. |
|**eventHubName** |**EventHubName** | Funkcje 2. x i nowsze. Nazwa centrum zdarzeń. Gdy nazwa centrum zdarzeń jest również obecna w parametrach połączenia, ta wartość zastępuje tę właściwość w czasie wykonywania. Można odwoływać się za pomocą [ustawień aplikacji](../articles/azure-functions/functions-bindings-expressions-patterns.md#binding-expressions---app-settings)`%eventHubName%` |
|**odbiorca** |**Odbiorca** | Opcjonalna właściwość, która ustawia [grupę odbiorców](../articles/event-hubs/event-hubs-features.md#event-consumers) służącą do subskrybowania zdarzeń w centrum. W przypadku pominięcia `$Default` zostanie użyta Grupa konsumentów. |
|**kardynalności** | n/d | Używane dla wszystkich języków innych niż C #. Ustaw wartość na, aby `many` włączyć przetwarzanie wsadowe.  W przypadku pominięcia lub ustawienia wartość do `one` funkcji zostaje przekazana jedna wiadomość.<br><br>W języku C# ta właściwość jest automatycznie przypisywana za każdym razem, gdy wyzwalacz ma tablicę dla tego typu.|
|**połączenia** |**Połączenie** | Nazwa ustawienia aplikacji, które zawiera parametry połączenia z przestrzenią nazw centrum zdarzeń. Skopiuj te parametry połączenia, klikając przycisk **Informacje o połączeniu** dla [obszaru nazw](../articles/event-hubs/event-hubs-create.md#create-an-event-hubs-namespace), a nie samego centrum zdarzeń. Te parametry połączenia muszą mieć co najmniej uprawnienia do odczytu w celu aktywowania wyzwalacza.<br><br>Jeśli używasz [wersji 5. x lub nowszej rozszerzenia](../articles/azure-functions/functions-bindings-event-hubs.md#event-hubs-extension-5x-and-higher)zamiast parametrów połączenia, możesz podać odwołanie do sekcji konfiguracji, która definiuje połączenie. Zobacz [połączenia](../articles/azure-functions/functions-reference.md#connections).|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Użycie

# <a name="c"></a>[C#](#tab/csharp)

### <a name="default"></a>Domyślne

Dla wyzwalającego centrum zdarzeń można użyć następujących typów parametrów:

* `string`
* `byte[]`
* `POCO`
* `EventData` -Domyślne właściwości EventData są podane w [przestrzeni nazw Microsoft. Azure. EventHubs](/dotnet/api/microsoft.azure.eventhubs.eventdata?view=azure-dotnet).

### <a name="additional-types"></a>Dodatkowe typy 
Aplikacje korzystające z 5.0.0 lub nowszej wersji rozszerzenia centrum zdarzeń używają `EventData` typu na [platformie Azure. Messaging. EventHubs](/dotnet/api/azure.messaging.eventhubs.eventdata?view=azure-dotnet) zamiast w ramach [przestrzeni nazw Microsoft. Azure. EventHubs](/dotnet/api/microsoft.azure.eventhubs.eventdata?view=azure-dotnet). Ta wersja porzuca obsługę starszego `Body` typu na korzyść następujących typów:

- [EventBody](/dotnet/api/azure.messaging.eventhubs.eventdata.eventbody?view=azure-dotnet)

# <a name="c-script"></a>[Skrypt C#](#tab/csharp-script)

### <a name="default"></a>Domyślne

Dla wyzwalającego centrum zdarzeń można użyć następujących typów parametrów:

* `string`
* `byte[]`
* `POCO`
* `EventData` -Domyślne właściwości EventData są podane w [przestrzeni nazw Microsoft. Azure. EventHubs](/dotnet/api/microsoft.azure.eventhubs.eventdata?view=azure-dotnet).

### <a name="additional-types"></a>Dodatkowe typy 
Aplikacje korzystające z 5.0.0 lub nowszej wersji rozszerzenia centrum zdarzeń używają `EventData` typu na [platformie Azure. Messaging. EventHubs](/dotnet/api/azure.messaging.eventhubs.eventdata?view=azure-dotnet) zamiast w ramach [przestrzeni nazw Microsoft. Azure. EventHubs](/dotnet/api/microsoft.azure.eventhubs.eventdata?view=azure-dotnet). Ta wersja porzuca obsługę starszego `Body` typu na korzyść następujących typów:

- [EventBody](/dotnet/api/azure.messaging.eventhubs.eventdata.eventbody?view=azure-dotnet)

# <a name="java"></a>[Java](#tab/java)

Aby uzyskać szczegółowe informacje, zapoznaj się z [przykładem wyzwalacza](#example) Java.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Aby uzyskać szczegółowe informacje, zapoznaj się z [przykładem wyzwalacza](#example) JavaScript.

# <a name="python"></a>[Python](#tab/python)

Zapoznaj się z [przykładem wyzwalacza](#example) języka Python, aby uzyskać szczegółowe informacje.


---


## <a name="event-metadata"></a>Metadane zdarzenia

Wyzwalacz Event Hubs zawiera kilka [właściwości metadanych](../articles/azure-functions/./functions-bindings-expressions-patterns.md). Właściwości metadanych mogą służyć jako część wyrażeń powiązań w innych powiązaniach lub jako parametry w kodzie. Właściwości pochodzą z klasy [EVENTDATA](/dotnet/api/microsoft.servicebus.messaging.eventdata) .

|Właściwość|Typ|Opis|
|--------|----|-----------|
|`PartitionContext`|[PartitionContext](/dotnet/api/microsoft.servicebus.messaging.partitioncontext)|Wystąpienie elementu `PartitionContext`.|
|`EnqueuedTimeUtc`|`DateTime`|Czas w kolejce w formacie UTC.|
|`Offset`|`string`|Przesunięcie danych względem strumienia partycji centrum zdarzeń. Przesunięcie jest znacznikiem lub identyfikatorem zdarzenia w strumieniu Event Hubs. Identyfikator jest unikatowy w obrębie partycji Event Hubs strumienia.|
|`PartitionKey`|`string`|Partycja, do której mają być wysyłane dane zdarzeń.|
|`Properties`|`IDictionary<String,Object>`|Właściwości użytkownika danych zdarzenia.|
|`SequenceNumber`|`Int64`|Numer sekwencji logicznej zdarzenia.|
|`SystemProperties`|`IDictionary<String,Object>`|Właściwości systemu, w tym dane zdarzenia.|

Zobacz [przykłady kodu](#example) , które używają tych właściwości wcześniej w tym artykule.
