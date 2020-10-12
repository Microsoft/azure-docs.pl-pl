---
title: Wyzwalacz usługi Azure queue storage dla Azure Functions
description: Dowiedz się, jak uruchamiać funkcję platformy Azure jako zmiany danych usługi Azure queue storage.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: devx-track-csharp, cc996988-fb4f-47, devx-track-python
ms.openlocfilehash: 01021530c491fd25a199f32475c031a0e7f6cd0b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89376642"
---
# <a name="azure-queue-storage-trigger-for-azure-functions"></a>Wyzwalacz usługi Azure queue storage dla Azure Functions

Wyzwalacz magazynu kolejki uruchamia funkcję, gdy do usługi Azure queue storage są dodawane komunikaty.

## <a name="encoding"></a>Encoding

Funkcja oczekuje ciągu zakodowanego w *formacie base64* . Wszelkie zmiany typu kodowania (w celu przygotowania danych jako ciągu zakodowanego *algorytmem Base64* ) muszą zostać zaimplementowane w usłudze wywołującej.

## <a name="example"></a>Przykład

Użyj wyzwalacza kolejki, aby uruchomić funkcję po odebraniu nowego elementu w kolejce. Komunikat w kolejce jest przekazywany do funkcji jako dane wejściowe.

# <a name="c"></a>[C#](#tab/csharp)

Poniższy przykład pokazuje [funkcję języka C#](functions-dotnet-class-library.md) , która sonduje `myqueue-items` kolejkę i zapisuje dziennik za każdym razem, gdy element kolejki jest przetwarzany.

```csharp
public static class QueueFunctions
{
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        ILogger log)
    {
        log.LogInformation($"C# function processed: {myQueueItem}");
    }
}
```

# <a name="c-script"></a>[Skrypt C#](#tab/csharp-script)

Poniższy przykład przedstawia powiązanie wyzwalacza kolejki w *function.jsw* kodzie pliku i [skrypcie C# (CSX)](functions-reference-csharp.md) , który używa powiązania. Funkcja sonduje `myqueue-items` kolejkę i zapisuje dziennik za każdym razem, gdy element kolejki jest przetwarzany.

Oto *function.js* pliku:

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ]
}
```

W sekcji [Konfiguracja](#configuration) objaśniono te właściwości.

Oto kod skryptu w języku C#:

```csharp
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.Extensions.Logging;
using Microsoft.WindowsAzure.Storage.Queue;
using System;

public static void Run(CloudQueueMessage myQueueItem, 
    DateTimeOffset expirationTime, 
    DateTimeOffset insertionTime, 
    DateTimeOffset nextVisibleTime,
    string queueTrigger,
    string id,
    string popReceipt,
    int dequeueCount,
    ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem.AsString}\n" +
        $"queueTrigger={queueTrigger}\n" +
        $"expirationTime={expirationTime}\n" +
        $"insertionTime={insertionTime}\n" +
        $"nextVisibleTime={nextVisibleTime}\n" +
        $"id={id}\n" +
        $"popReceipt={popReceipt}\n" + 
        $"dequeueCount={dequeueCount}");
}
```

W sekcji [użycie](#usage) objaśniono `myQueueItem` , że nazwa jest określana przez `name` Właściwość w function.json.  W [sekcji metadane wiadomości](#message-metadata) objaśniono wszystkie inne wyświetlane zmienne.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Poniższy przykład pokazuje powiązanie wyzwalacza kolejki w *function.jsw* pliku oraz [funkcja języka JavaScript](functions-reference-node.md) , która używa powiązania. Funkcja sonduje `myqueue-items` kolejkę i zapisuje dziennik za każdym razem, gdy element kolejki jest przetwarzany.

Oto *function.js* pliku:

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ]
}
```

W sekcji [Konfiguracja](#configuration) objaśniono te właściwości.

> [!NOTE]
> Parametr name odzwierciedla jak `context.bindings.<name>` w kodzie JavaScript, który zawiera ładunek elementu kolejki. Ten ładunek jest również przenoszona jako drugi parametr do funkcji.

Oto kod JavaScript:

```javascript
module.exports = async function (context, message) {
    context.log('Node.js queue trigger function processed work item', message);
    // OR access using context.bindings.<name>
    // context.log('Node.js queue trigger function processed work item', context.bindings.myQueueItem);
    context.log('expirationTime =', context.bindingData.expirationTime);
    context.log('insertionTime =', context.bindingData.insertionTime);
    context.log('nextVisibleTime =', context.bindingData.nextVisibleTime);
    context.log('id =', context.bindingData.id);
    context.log('popReceipt =', context.bindingData.popReceipt);
    context.log('dequeueCount =', context.bindingData.dequeueCount);
    context.done();
};
```

W sekcji [użycie](#usage) objaśniono `myQueueItem` , że nazwa jest określana przez `name` Właściwość w function.json.  W [sekcji metadane wiadomości](#message-metadata) objaśniono wszystkie inne wyświetlane zmienne.

# <a name="python"></a>[Python](#tab/python)

Poniższy przykład ilustruje sposób odczytywania komunikatu kolejki przekazanego do funkcji za pośrednictwem wyzwalacza.

Wyzwalacz kolejki magazynu jest zdefiniowany w *function.jsna* miejscu, w którym ustawiono *Typ* `queueTrigger` .

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "msg",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "messages",
      "connection": "AzureStorageQueuesConnectionString"
    }
  ]
}
```

Kod * _ \_ init_ \_ . PR* deklaruje parametr AS `func.QueueMessage` , który umożliwia odczytywanie komunikatu w kolejce w funkcji.

```python
import logging
import json

import azure.functions as func

def main(msg: func.QueueMessage):
    logging.info('Python queue trigger function processed a queue item.')

    result = json.dumps({
        'id': msg.id,
        'body': msg.get_body().decode('utf-8'),
        'expiration_time': (msg.expiration_time.isoformat()
                            if msg.expiration_time else None),
        'insertion_time': (msg.insertion_time.isoformat()
                           if msg.insertion_time else None),
        'time_next_visible': (msg.time_next_visible.isoformat()
                              if msg.time_next_visible else None),
        'pop_receipt': msg.pop_receipt,
        'dequeue_count': msg.dequeue_count
    })

    logging.info(result)
```

# <a name="java"></a>[Java](#tab/java)

Poniższy przykład Java pokazuje funkcję wyzwalacza kolejki magazynu, która rejestruje wyzwolony komunikat umieszczony w kolejce `myqueuename` .

 ```java
 @FunctionName("queueprocessor")
 public void run(
    @QueueTrigger(name = "msg",
                   queueName = "myqueuename",
                   connection = "myconnvarname") String message,
     final ExecutionContext context
 ) {
     context.getLogger().info(message);
 }
 ```

 ---

## <a name="attributes-and-annotations"></a>Atrybuty i adnotacje

# <a name="c"></a>[C#](#tab/csharp)

W [bibliotekach klas języka C#](functions-dotnet-class-library.md)Użyj następujących atrybutów, aby skonfigurować wyzwalacz kolejki:

* [QueueTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Queues/QueueTriggerAttribute.cs)

  Konstruktor atrybutu przyjmuje nazwę kolejki do monitorowania, jak pokazano w następującym przykładzie:

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items")] string myQueueItem, 
      ILogger log)
  {
      ...
  }
  ```

  Można ustawić `Connection` Właściwość, aby określić ustawienie aplikacji, które zawiera parametry połączenia konta magazynu do użycia, jak pokazano w następującym przykładzie:

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items", Connection = "StorageConnectionAppSetting")] string myQueueItem, 
      ILogger log)
  {
      ....
  }
  ```

  Aby zapoznać się z pełnym przykładem, zobacz [przykład](#example).

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Zapewnia inny sposób określania konta magazynu, które ma być używane. Konstruktor przyjmuje nazwę ustawienia aplikacji, które zawiera parametry połączenia magazynu. Ten atrybut może być stosowany na poziomie parametru, metody lub klasy. W poniższym przykładzie przedstawiono poziom klasy i poziom metody:

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("QueueTrigger")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ...
  }
  ```

Konto magazynu do użycia jest określane w następującej kolejności:

* `QueueTrigger` `Connection` Właściwość atrybutu.
* `StorageAccount`Atrybut zastosowany do tego samego parametru, który jest `QueueTrigger` atrybutem.
* `StorageAccount`Atrybut zastosowany do funkcji.
* `StorageAccount`Atrybut zastosowany do klasy.
* Ustawienie aplikacji "AzureWebJobsStorage".

# <a name="c-script"></a>[Skrypt C#](#tab/csharp-script)

Atrybuty nie są obsługiwane przez skrypt języka C#.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Atrybuty nie są obsługiwane przez język JavaScript.

# <a name="python"></a>[Python](#tab/python)

Atrybuty nie są obsługiwane przez język Python.

# <a name="java"></a>[Java](#tab/java)

`QueueTrigger`Adnotacja zapewnia dostęp do kolejki, która wyzwala funkcję. Poniższy przykład powoduje, że komunikat kolejki jest dostępny dla funkcji za pośrednictwem `message` parametru.

```java
package com.function;
import com.microsoft.azure.functions.annotation.*;
import java.util.Queue;
import com.microsoft.azure.functions.*;

public class QueueTriggerDemo {
    @FunctionName("QueueTriggerDemo")
    public void run(
        @QueueTrigger(name = "message", queueName = "messages", connection = "MyStorageConnectionAppSetting") String message,
        final ExecutionContext context
    ) {
        context.getLogger().info("Queue message: " + message);
    }
}
```

| Właściwość    | Opis |
|-------------|-----------------------------|
|`name`       | Deklaruje nazwę parametru w podpisie funkcji. Gdy funkcja jest wyzwalana, wartość tego parametru ma zawartość komunikatu w kolejce. |
|`queueName`  | Deklaruje nazwę kolejki na koncie magazynu. |
|`connection` | Wskazuje na parametry połączenia konta magazynu. |

---

## <a name="configuration"></a>Konfiguracja

W poniższej tabeli objaśniono właściwości konfiguracji powiązań, które zostały ustawione w *function.js* pliku i `QueueTrigger` atrybutu.

|function.jswłaściwości | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**Wprowadź** | nie dotyczy| Musi być ustawiony na `queueTrigger` . Ta właściwość jest ustawiana automatycznie podczas tworzenia wyzwalacza w Azure Portal.|
|**wskazywa**| nie dotyczy | W *function.jstylko na* pliku. Musi być ustawiony na `in` . Ta właściwość jest ustawiana automatycznie podczas tworzenia wyzwalacza w Azure Portal. |
|**Nazwij** | nie dotyczy |Nazwa zmiennej, która zawiera ładunek elementu kolejki w kodzie funkcji.  |
|**Zmienną QueueName** | **Zmienną QueueName**| Nazwa kolejki do sondowania. |
|**połączenia** | **Połączenie** |Nazwa ustawienia aplikacji, które zawiera parametry połączenia magazynu, które będą używane dla tego powiązania. Jeśli nazwa ustawienia aplikacji zaczyna się od "AzureWebJobs", w tym miejscu możesz określić tylko resztę nazwy. Jeśli na przykład ustawisz opcję `connection` "Moja magazyn", środowisko uruchomieniowe funkcji wyszukuje ustawienie aplikacji o nazwie "WebStorage". Jeśli pozostawisz `connection` puste, środowisko uruchomieniowe funkcji używa domyślnych parametrów połączenia magazynu w ustawieniu aplikacji o nazwie `AzureWebJobsStorage` .|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Użycie

# <a name="c"></a>[C#](#tab/csharp)

Uzyskaj dostęp do danych komunikatów przy użyciu parametru metody, takiego jak `string paramName` . Można powiązać z dowolnym z następujących typów:

* Object — środowisko uruchomieniowe funkcji deserializacji ładunek JSON do wystąpienia dowolnej klasy zdefiniowanej w kodzie. 
* `string`
* `byte[]`
* [CloudQueueMessage]

Jeśli próbujesz powiązać z `CloudQueueMessage` i otrzymać komunikat o błędzie, upewnij się, że masz odwołanie do [odpowiedniej wersji zestawu SDK magazynu](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x).

# <a name="c-script"></a>[Skrypt C#](#tab/csharp-script)

Uzyskaj dostęp do danych komunikatów przy użyciu parametru metody, takiego jak `string paramName` . `paramName`Jest wartością określoną we `name` właściwości *function.jsna*. Można powiązać z dowolnym z następujących typów:

* Object — środowisko uruchomieniowe funkcji deserializacji ładunek JSON do wystąpienia dowolnej klasy zdefiniowanej w kodzie. 
* `string`
* `byte[]`
* [CloudQueueMessage]

Jeśli próbujesz powiązać z `CloudQueueMessage` i otrzymać komunikat o błędzie, upewnij się, że masz odwołanie do [odpowiedniej wersji zestawu SDK magazynu](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Ładunek elementu kolejki jest dostępny za pośrednictwem `context.bindings.<NAME>` Where `<NAME>` , gdzie pasuje do nazwy zdefiniowanej w *function.jsna*. Jeśli ładunek jest w formacie JSON, wartość jest deserializowana do obiektu.

# <a name="python"></a>[Python](#tab/python)

Uzyskaj dostęp do komunikatu kolejki za pośrednictwem parametru, który został określony jako [QueueMessage](/python/api/azure-functions/azure.functions.queuemessage?view=azure-python).

# <a name="java"></a>[Java](#tab/java)

Adnotacja [QueueTrigger](/java/api/com.microsoft.azure.functions.annotation.queuetrigger?view=azure-java-stable) daje dostęp do komunikatu kolejki, który wyzwolił funkcję.

---

## <a name="message-metadata"></a>Metadane komunikatów

Wyzwalacz kolejki zawiera kilka [właściwości metadanych](./functions-bindings-expressions-patterns.md#trigger-metadata). Te właściwości mogą służyć jako część wyrażeń powiązań w innych powiązaniach lub jako parametry w kodzie. Właściwości są członkami klasy [CloudQueueMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage) .

|Właściwość|Type|Opis|
|--------|----|-----------|
|`QueueTrigger`|`string`|Ładunek kolejki (w przypadku prawidłowego ciągu). Jeśli ładunek komunikatu w kolejce jest ciągiem, `QueueTrigger` ma taką samą wartość jak zmienna o nazwie określona przez `name` właściwość w *function.js*.|
|`DequeueCount`|`int`|Liczba przypadków, w których ten komunikat został usunięty z kolejki.|
|`ExpirationTime`|`DateTimeOffset`|Godzina wygaśnięcia komunikatu.|
|`Id`|`string`|Identyfikator komunikatu kolejki.|
|`InsertionTime`|`DateTimeOffset`|Godzina, o której wiadomość została dodana do kolejki.|
|`NextVisibleTime`|`DateTimeOffset`|Godzina, o której komunikat będzie dalej widoczny.|
|`PopReceipt`|`string`|Potwierdzenie wyskakujące komunikatu.|

## <a name="poison-messages"></a>Trujące komunikaty

Gdy funkcja wyzwalacza kolejki kończy się niepowodzeniem, Azure Functions ponawia próbę wykonania funkcji maksymalnie pięć razy dla danego komunikatu kolejki, łącznie z pierwszą próbą. Jeśli wszystkie pięć prób nie powiedzie się, środowisko uruchomieniowe funkcji dodaje komunikat do kolejki o nazwie * &lt; originalqueuename>-Trująca*. Funkcję można napisać, aby przetwarzać komunikaty z kolejki trującej, rejestrując je lub wysyłając powiadomienie, które wymaga ręcznej uwagi.

Aby ręcznie obsłużyć trujące komunikaty, sprawdź [dequeueCount](#message-metadata) komunikatu w kolejce.

## <a name="polling-algorithm"></a>Algorytm sondowania

Wyzwalacz kolejki implementuje losowy wykładniczy algorytm wycofywania, aby zmniejszyć wpływ sondowania w kolejce na koszty transakcji magazynu.

Algorytm używa następującej logiki:

- Po znalezieniu komunikatu środowisko uruchomieniowe czeka dwa sekundy, a następnie sprawdza inny komunikat
- Gdy nie zostanie znaleziony żaden komunikat, czeka około czterech sekund przed ponowieniem próby.
- Po kolejnych nieudanych próbach pobrania komunikatu w kolejce czas oczekiwania będzie się zwiększać do momentu osiągnięcia maksymalnego czasu oczekiwania, który jest wartością domyślną jednej minuty.
- Maksymalny czas oczekiwania można skonfigurować za pomocą `maxPollingInterval` właściwości w [host.jspliku](functions-host-json-v1.md#queues).

W przypadku lokalnego tworzenia maksymalnego interwału sondowania wartość domyślna to dwie sekundy.

W odniesieniu do rozliczania czas spędzony na sondowaniu przez środowisko uruchomieniowe jest "bezpłatny" i nie jest liczony na koncie.

## <a name="concurrency"></a>Współbieżność

Gdy oczekuje wiele komunikatów w kolejce, wyzwalacz kolejki pobiera partię komunikatów i wywołuje wystąpienia funkcji współbieżnie, aby je przetworzyć. Domyślnie rozmiar wsadu wynosi 16. Gdy przetworzona liczba jest równa 8, środowisko uruchomieniowe pobiera kolejną partię i uruchamia przetwarzanie tych komunikatów. Maksymalna liczba współbieżnych komunikatów przetwarzanych na funkcję na jednej maszynie wirtualnej wynosi 24. Ten limit dotyczy osobno każdej funkcji wyzwalanej przez kolejkę na każdej maszynie wirtualnej. Jeśli aplikacja funkcji jest skalowana do wielu maszyn wirtualnych, każda maszyna wirtualna będzie czekać na wyzwalacze i próbować uruchamiać funkcje. Na przykład jeśli aplikacja funkcji jest skalowana do 3 maszyn wirtualnych, domyślna maksymalna liczba równoczesnych wystąpień jednej funkcji wyzwalanej przez kolejkę to 72.

Rozmiar partii i próg pobierania nowej partii można skonfigurować w [host.jsna pliku](functions-host-json.md#queues). Jeśli chcesz zminimalizować wykonywanie równoległe dla funkcji wyzwalanych przez kolejki w aplikacji funkcji, możesz ustawić rozmiar wsadu na 1. To ustawienie eliminuje współbieżność tylko wtedy, gdy aplikacja funkcji działa na jednej maszynie wirtualnej (VM). 

Wyzwalacz kolejki automatycznie zapobiega przetwarzaniu komunikatu kolejki wielokrotnie jednocześnie.

## <a name="hostjson-properties"></a>host.jswłaściwości

[host.jsw](functions-host-json.md#queues) pliku zawiera ustawienia sterujące zachowaniem wyzwalacza kolejki. Aby uzyskać szczegółowe informacje na temat dostępnych ustawień, zobacz sekcję [host.jsw ustawieniach](functions-bindings-storage-queue-output.md#hostjson-settings) .

## <a name="next-steps"></a>Następne kroki

- [Zapisywanie komunikatów magazynu obiektów BLOB (powiązanie danych wyjściowych)](./functions-bindings-storage-blob-output.md)

<!-- LINKS -->

[CloudQueueMessage]: /dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage
