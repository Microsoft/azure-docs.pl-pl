---
title: Przykład wyzwalacza i powiązania usługi Azure Functions
description: Dowiedz się, jak skonfigurować powiązania funkcji platformy Azure
author: craigshoemaker
ms.topic: reference
ms.custom: devx-track-csharp
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: c95524a5de6696bd0ffe7463451d152a9d3a19b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88205966"
---
# <a name="azure-functions-trigger-and-binding-example"></a>Przykład wyzwalacza i powiązania usługi Azure Functions

W tym artykule pokazano, jak skonfigurować [wyzwalacz i powiązania](./functions-triggers-bindings.md) w funkcji platformy Azure.

Załóżmy, że chcesz napisać nowy wiersz do usługi Azure Table Storage za każdym razem, gdy w usłudze Azure queue storage zostanie wyświetlony nowy komunikat. Taki scenariusz można zaimplementować przy użyciu wyzwalacza usługi Azure Queue Storage i powiązania danych wyjściowych usługi Azure Table Storage. 

Otofunction.jspliku *w* tym scenariuszu. 

```json
{
  "bindings": [
    {
      "type": "queueTrigger",
      "direction": "in",
      "name": "order",
      "queueName": "myqueue-items",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    },
    {
      "type": "table",
      "direction": "out",
      "name": "$return",
      "tableName": "outTable",
      "connection": "MY_TABLE_STORAGE_ACCT_APP_SETTING"
    }
  ]
}
```

Pierwszy element w `bindings` tablicy jest wyzwalaczem magazynu kolejki. `type`Właściwości i `direction` identyfikują wyzwalacz. `name`Właściwość identyfikuje parametr funkcji, który odbiera zawartość komunikatu w kolejce. Nazwa kolejki do monitorowania znajduje się w `queueName` , a parametry połączenia są w ustawieniu aplikacji identyfikowanym przez `connection` .

Drugi element w `bindings` tablicy to powiązanie danych wyjściowych usługi Azure Table Storage. `type`Właściwości i `direction` identyfikują powiązanie. `name`Właściwość określa, jak funkcja udostępnia nowy wiersz tabeli, w tym przypadku przy użyciu wartości zwracanej funkcji. Nazwa tabeli znajduje się w `tableName` , a parametry połączenia są w ustawieniu aplikacji identyfikowanym przez `connection` .

Aby wyświetlić i edytować zawartość *function.js* w Azure Portal, kliknij opcję **Edytor zaawansowany** na karcie **integracja** funkcji.

> [!NOTE]
> Wartość `connection` to nazwa ustawienia aplikacji, które zawiera parametry połączenia, a nie same parametry połączenia. Powiązania używają parametrów połączenia przechowywanych w ustawieniach aplikacji, aby wymusić najlepsze rozwiązanie, że *function.jsw* programie nie zawiera wpisów tajnych usługi.

## <a name="c-script-example"></a>Przykładowy skrypt C#

Oto kod skryptu języka C#, który działa z tym wyzwalaczem i powiązaniem. Należy zauważyć, że nazwa parametru dostarczającego zawartość komunikatu kolejki to `order` . Ta nazwa jest wymagana, ponieważ `name` wartość właściwości w *function.js* jest `order` 

```cs
#r "Newtonsoft.Json"

using Microsoft.Extensions.Logging;
using Newtonsoft.Json.Linq;

// From an incoming queue message that is a JSON object, add fields and write to Table storage
// The method return value creates a new row in Table Storage
public static Person Run(JObject order, ILogger log)
{
    return new Person() { 
            PartitionKey = "Orders", 
            RowKey = Guid.NewGuid().ToString(),  
            Name = order["Name"].ToString(),
            MobileNumber = order["MobileNumber"].ToString() };  
}
 
public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
    public string MobileNumber { get; set; }
}
```

## <a name="javascript-example"></a>Przykład JavaScript

Ten sam *function.jsw* pliku może być używany z funkcją języka JavaScript:

```javascript
// From an incoming queue message that is a JSON object, add fields and write to Table Storage
// The second parameter to context.done is used as the value for the new row
module.exports = function (context, order) {
    order.PartitionKey = "Orders";
    order.RowKey = generateRandomId(); 

    context.done(null, order);
};

function generateRandomId() {
    return Math.random().toString(36).substring(2, 15) +
        Math.random().toString(36).substring(2, 15);
}
```

## <a name="class-library-example"></a>Przykład biblioteki klas

W bibliotece klas ten sam wyzwalacz i kolejka informacji o powiązaniu i &mdash; Nazwa tabeli, konta magazynu i parametry funkcji dla danych wejściowych i wyjściowych &mdash; są udostępniane przez atrybuty zamiast function.jsw pliku. Oto przykład:

```csharp
public static class QueueTriggerTableOutput
{
    [FunctionName("QueueTriggerTableOutput")]
    [return: Table("outTable", Connection = "MY_TABLE_STORAGE_ACCT_APP_SETTING")]
    public static Person Run(
        [QueueTrigger("myqueue-items", Connection = "MY_STORAGE_ACCT_APP_SETTING")]JObject order,
        ILogger log)
    {
        return new Person() {
                PartitionKey = "Orders",
                RowKey = Guid.NewGuid().ToString(),
                Name = order["Name"].ToString(),
                MobileNumber = order["MobileNumber"].ToString() };
    }
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
    public string MobileNumber { get; set; }
}
```

Masz teraz działającą funkcję, która jest wyzwalana przez kolejkę platformy Azure i wyprowadza dane z usługi Azure Table Storage.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wzorce wyrażeń powiązań Azure Functions](./functions-bindings-expressions-patterns.md)
