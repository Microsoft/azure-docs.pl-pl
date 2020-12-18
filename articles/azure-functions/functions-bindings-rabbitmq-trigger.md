---
title: Wyzwalacz RabbitMQ dla Azure Functions
description: Dowiedz się, jak uruchomić funkcję platformy Azure po utworzeniu komunikatu RabbitMQ.
author: cachai2
ms.assetid: ''
ms.topic: reference
ms.date: 12/17/2020
ms.author: cachai
ms.custom: ''
ms.openlocfilehash: 5930219486de8704c777496bcaf293411c5fb7b1
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/18/2020
ms.locfileid: "97673991"
---
# <a name="rabbitmq-trigger-for-azure-functions-overview"></a>Wyzwalacz RabbitMQ dla Azure Functions przegląd

> [!NOTE]
> Powiązania RabbitMQ są w pełni obsługiwane tylko w planach **systemu Windows Premium i dedykowanych** . Użycie i system Linux nie są obecnie obsługiwane.

Użyj wyzwalacza RabbitMQ, aby odpowiedzieć na komunikaty z kolejki RabbitMQ.

Aby uzyskać informacje na temat konfiguracji i szczegółów konfiguracji, zobacz [Omówienie](functions-bindings-rabbitmq.md).

## <a name="example"></a>Przykład

# <a name="c"></a>[C#](#tab/csharp)

Poniższy przykład pokazuje [funkcję języka C#](functions-dotnet-class-library.md) , która odczytuje i rejestruje komunikat RabbitMQ jako [zdarzenie RabbitMQ](https://www.rabbitmq.com/releases/rabbitmq-dotnet-client/v3.2.2/rabbitmq-dotnet-client-3.2.2-client-htmldoc/html/type-RabbitMQ.Client.Events.BasicDeliverEventArgs.html):

```cs
[FunctionName("RabbitMQTriggerCSharp")]
public static void RabbitMQTrigger_BasicDeliverEventArgs(
    [RabbitMQTrigger("queue", ConnectionStringSetting = "rabbitMQConnectionAppSetting")] BasicDeliverEventArgs args,
    ILogger logger
    )
{
    logger.LogInformation($"C# RabbitMQ queue trigger function processed message: {Encoding.UTF8.GetString(args.Body)}");
}
```

Poniższy przykład pokazuje, jak odczytać komunikat jako POCO.

```cs
public class TestClass
{
    public string x { get; set; }
}

[FunctionName("RabbitMQTriggerCSharp")]
public static void RabbitMQTrigger_BasicDeliverEventArgs(
    [RabbitMQTrigger("queue", ConnectionStringSetting = "rabbitMQConnectionAppSetting")] TestClass pocObj,
    ILogger logger
    )
{
    logger.LogInformation($"C# RabbitMQ queue trigger function processed message: {Encoding.UTF8.GetString(pocObj)}");
}
```

Podobnie jak w przypadku obiektów JSON, wystąpi błąd, jeśli komunikat nie jest prawidłowo sformatowany jako obiekt języka C#. Jeśli tak jest, jest on następnie powiązany ze zmienną pocObj, która może być używana w przypadku, gdy jest to konieczne.

# <a name="c-script"></a>[Skrypt C#](#tab/csharp-script)

Poniższy przykład przedstawia powiązanie wyzwalacza RabbitMQ w *function.jsw* pliku i [funkcję skryptu języka C#](functions-reference-csharp.md) , która używa powiązania. Funkcja odczytuje i rejestruje komunikat RabbitMQ.

Oto dane powiązania w *function.js* pliku:

```json
{
    "bindings": [
        {
            "name": "myQueueItem",
            "type": "rabbitMQTrigger",
            "direction": "in",
            "queueName": "queue",
            "connectionStringSetting": "rabbitMQConnectionAppSetting"
        }
    ]
}
```

Oto kod skryptu w języku C#:

```csx
using System;

public static void Run(string myQueueItem, ILogger log)
{
    log.LogInformation($"C# Script RabbitMQ trigger function processed: {myQueueItem}");
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Poniższy przykład przedstawia powiązanie wyzwalacza RabbitMQ w *function.jsw* pliku oraz [funkcja języka JavaScript](functions-reference-node.md) , która używa powiązania. Funkcja odczytuje i rejestruje komunikat RabbitMQ.

Oto dane powiązania w *function.js* pliku:

```json
{
    "bindings": [
        {
            "name": "myQueueItem",
            "type": "rabbitMQTrigger",
            "direction": "in",
            "queueName": "queue",
            "connectionStringSetting": "rabbitMQConnectionAppSetting"
        }
    ]
}
```

Oto kod skryptu JavaScript:

```javascript
module.exports = async function (context, myQueueItem) {
    context.log('JavaScript RabbitMQ trigger function processed work item', myQueueItem);
};
```

# <a name="python"></a>[Python](#tab/python)

Poniższy przykład ilustruje sposób odczytywania komunikatu kolejki RabbitMQ za pośrednictwem wyzwalacza.

Powiązanie RabbitMQ jest zdefiniowane w *function.jsna* miejscu, w którym ustawiono *Typ* `RabbitMQTrigger` .

```json
{
    "scriptFile": "__init__.py",
    "bindings": [
        {
            "name": "myQueueItem",
            "type": "rabbitMQTrigger",
            "direction": "in",
            "queueName": "queue",
            "connectionStringSetting": "rabbitMQConnectionAppSetting"
        }
    ]
}
```

```python
import logging
import azure.functions as func

def main(myQueueItem) -> None:
    logging.info('Python RabbitMQ trigger function processed a queue item: %s', myQueueItem)
```

# <a name="java"></a>[Java](#tab/java)

Następująca funkcja języka Java używa `@RabbitMQTrigger` adnotacji z [typów RabbitMQ języka Java](https://mvnrepository.com/artifact/com.microsoft.azure.functions/azure-functions-java-library-rabbitmq) do opisywania konfiguracji dla wyzwalacza kolejki RabbitMQ. Funkcja poniesie komunikat umieszczony w kolejce i dodaje ją do dzienników.

```java
@FunctionName("RabbitMQTriggerExample")
public void run(
    @RabbitMQTrigger(connectionStringSetting = "rabbitMQConnectionAppSetting", queueName = "queue") String input,
    final ExecutionContext context)
{
    context.getLogger().info("Java HTTP trigger processed a request." + input);
}
```

---

## <a name="attributes-and-annotations"></a>Atrybuty i adnotacje

# <a name="c"></a>[C#](#tab/csharp)

W [bibliotekach klas języka C#](functions-dotnet-class-library.md), Użyj atrybutu [RabbitMQTrigger](https://github.com/Azure/azure-functions-rabbitmq-extension/blob/dev/src/Trigger/RabbitMQTriggerAttribute.cs) .

Oto `RabbitMQTrigger` atrybut w sygnaturze metody:

```csharp
[FunctionName("RabbitMQTest")]
public static void RabbitMQTest([RabbitMQTrigger("queue")] string message, ILogger log)
{
    ...
}
```

Aby zapoznać się z kompletnym przykładem, zobacz [przykład](#example)w języku C#.

# <a name="c-script"></a>[Skrypt C#](#tab/csharp-script)

Atrybuty nie są obsługiwane przez skrypt języka C#.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Atrybuty nie są obsługiwane przez język JavaScript.

# <a name="python"></a>[Python](#tab/python)

Atrybuty nie są obsługiwane przez język Python.

# <a name="java"></a>[Java](#tab/java)

`RabbitMQTrigger`Adnotacja pozwala utworzyć funkcję, która jest uruchamiana po utworzeniu komunikatu RabbitMQ. Dostępne opcje konfiguracji obejmują nazwę kolejki i nazwę parametrów połączenia. Aby uzyskać dodatkowe informacje o parametrach, odwiedź [Adnotacje języka Java RabbitMQTrigger](https://github.com/Azure/azure-functions-rabbitmq-extension/blob/dev/binding-library/java/src/main/java/com/microsoft/azure/functions/rabbitmq/annotation/RabbitMQTrigger.java).

Zobacz [przykład](#example) wyzwalacza, aby uzyskać więcej szczegółów.

---

## <a name="configuration"></a>Konfigurowanie

W poniższej tabeli objaśniono właściwości konfiguracji powiązań, które zostały ustawione w *function.js* pliku i `RabbitMQTrigger` atrybutu.

|function.jswłaściwości | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**Wprowadź** | n/d | Musi być ustawiona na wartość "RabbitMQTrigger".|
|**wskazywa** | n/d | Musi być ustawiona na wartość "in".|
|**Nazwij** | n/d | Nazwa zmiennej, która reprezentuje kolejkę w kodzie funkcji. |
|**Zmienną QueueName**|**Zmienną QueueName**| Nazwa kolejki, z której mają zostać odebrane komunikaty. |
|**Nazw**|**Nazw**|(ignorowane, jeśli jest używany ConnectStringSetting) <br>Nazwa hosta kolejki (np.: 10.26.45.210)|
|**userNameSetting**|**UserNameSetting**|(ignorowane, jeśli jest używany ConnectionStringSetting) <br>Nazwa ustawienia aplikacji, która zawiera nazwę użytkownika, aby uzyskać dostęp do kolejki. Np. UserNameSetting: "% < UserNameFromSettings >%"|
|**passwordSetting**|**PasswordSetting**|(ignorowane, jeśli jest używany ConnectionStringSetting) <br>Nazwa ustawienia aplikacji, która zawiera hasło umożliwiające dostęp do kolejki. Np. PasswordSetting: "% < PasswordFromSettings >%"|
|**connectionStringSetting**|**ConnectionStringSetting**|Nazwa ustawienia aplikacji, które zawiera parametry połączenia kolejki komunikatów RabbitMQ. Należy pamiętać, że w przypadku określenia parametrów połączenia bezpośrednio, a nie za pomocą ustawienia aplikacji w local.settings.jsna, wyzwalacz nie będzie działał. (Np.: w *function.jsna*: connectionStringSetting: "rabbitMQConnection" <br> W *local.settings.jsna*: "rabbitMQConnection": "< ActualConnectionstring >")|
|**przewożąc**|**Port**|(ignorowane, jeśli jest używany ConnectionStringSetting) Pobiera lub ustawia używany port. Wartość domyślna to 0.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Użycie

# <a name="c"></a>[C#](#tab/csharp)

Domyślny typ komunikatu to [zdarzenie RabbitMQ](https://www.rabbitmq.com/releases/rabbitmq-dotnet-client/v3.2.2/rabbitmq-dotnet-client-3.2.2-client-htmldoc/html/type-RabbitMQ.Client.Events.BasicDeliverEventArgs.html), a `Body` Właściwość zdarzenia RabbitMQ może zostać odczytana jako wymienione poniżej typy:

* `An object serializable as JSON` -Komunikat jest dostarczany jako prawidłowy ciąg JSON.
* `string`
* `byte[]`
* `POCO` -Komunikat jest sformatowany jako obiekt języka C#. Aby zapoznać się z kompletnym przykładem, zobacz [przykład](#example)w języku C#.

# <a name="c-script"></a>[Skrypt C#](#tab/csharp-script)

Domyślny typ komunikatu to [zdarzenie RabbitMQ](https://www.rabbitmq.com/releases/rabbitmq-dotnet-client/v3.2.2/rabbitmq-dotnet-client-3.2.2-client-htmldoc/html/type-RabbitMQ.Client.Events.BasicDeliverEventArgs.html), a `Body` Właściwość zdarzenia RabbitMQ może zostać odczytana jako wymienione poniżej typy:

* `An object serializable as JSON` -Komunikat jest dostarczany jako prawidłowy ciąg JSON.
* `string`
* `byte[]`
* `POCO` -Komunikat jest sformatowany jako obiekt języka C#. Aby zapoznać się z kompletnym przykładem, zobacz [przykład](#example)skryptu C#.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Komunikat kolejki jest dostępny za pośrednictwem kontekstu. powiązania.<NAME> gdzie <NAME> pasuje do nazwy zdefiniowanej w function.jsna. Jeśli ładunek jest w formacie JSON, wartość jest deserializowana do obiektu.

# <a name="python"></a>[Python](#tab/python)

Zapoznaj się z [przykładem](#example)w języku Python.

# <a name="java"></a>[Java](#tab/java)

Zapoznaj się z [atrybutami i adnotacjami](#attributes-and-annotations)języka Java.

---

## <a name="dead-letter-queues"></a>Kolejki utraconych wiadomości
Nie można sterować ani konfigurować kolejek utraconych wiadomości i wymian z poziomu wyzwalacza RabbitMQ.  Aby można było korzystać z kolejek utraconych wiadomości, należy wstępnie skonfigurować kolejkę używaną przez wyzwalacz w RabbitMQ. Zapoznaj się z [dokumentacją RabbitMQ](https://www.rabbitmq.com/dlx.html).

## <a name="hostjson-settings"></a>host.jsustawień

W tej sekcji opisano globalne ustawienia konfiguracji dostępne dla tego powiązania w wersji 2. x i nowszych. Przykład *host.jsw* pliku poniżej zawiera tylko ustawienia dla tego powiązania. Aby uzyskać więcej informacji na temat ustawień konfiguracji globalnej, zobacz [host.json Reference for Azure Functions Version](functions-host-json.md).

```json
{
    "version": "2.0",
    "extensions": {
        "rabbitMQ": {
            "prefetchCount": 100,
            "queueName": "queue",
            "connectionString": "amqp://user:password@url:port",
            "port": 10
        }
    }
}
```

|Właściwość  |Domyślny | Opis |
|---------|---------|---------|
|prefetchCount|30|Pobiera lub ustawia liczbę komunikatów, które odbiorca wiadomości może jednocześnie zażądać i jest w pamięci podręcznej.|
|Zmienną QueueName|n/d| Nazwa kolejki, z której mają zostać odebrane komunikaty.|
|Parametry połączenia|n/d|Parametry połączenia kolejki komunikatów RabbitMQ. Należy pamiętać, że parametry połączenia są bezpośrednio określone w tym miejscu, a nie za pomocą ustawienia aplikacji.|
|port|0|(ignorowane, jeśli jest używany ConnectionStringSetting) Pobiera lub ustawia używany port. Wartość domyślna to 0.|

## <a name="local-testing"></a>Testowanie lokalne

> [!NOTE]
> Parametr connectionString ma pierwszeństwo przed "hostName", "userName" i "Password". Jeśli są one ustawione, parametr connectionString przesłoni pozostałe dwa.

Jeśli testujesz lokalnie bez parametrów połączenia, należy ustawić ustawienie "hostName" i "userName" oraz "Password", jeśli ma to zastosowanie w sekcji "rabbitMQ" *host.jsna*:

```json
{
    "version": "2.0",
    "extensions": {
        "rabbitMQ": {
            ...
            "hostName": "localhost",
            "username": "userNameSetting",
            "password": "passwordSetting"
        }
    }
}
```

|Właściwość  |Domyślny | Opis |
|---------|---------|---------|
|Nazw|n/d|(ignorowane, jeśli jest używany ConnectStringSetting) <br>Nazwa hosta kolejki (np.: 10.26.45.210)|
|userName|n/d|(ignorowane, jeśli jest używany ConnectionStringSetting) <br>Nazwa dostępu do kolejki |
|hasło|n/d|(ignorowane, jeśli jest używany ConnectionStringSetting) <br>Hasło dostępu do kolejki|

## <a name="monitoring-rabbitmq-endpoint"></a>Punkt końcowy monitorowania RabbitMQ
Aby monitorować kolejki i wymianę dla pewnego punktu końcowego RabbitMQ:

* Włącz [wtyczkę zarządzania RabbitMQ](https://www.rabbitmq.com/management.html)
* Przejdź do protokołu http://{node-hostname}: 15672 i zaloguj się przy użyciu nazwy użytkownika i hasła.

## <a name="next-steps"></a>Następne kroki

- [Wysyłanie komunikatów RabbitMQ z Azure Functions (powiązanie danych wyjściowych)](./functions-bindings-rabbitmq-output.md)
